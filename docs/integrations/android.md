# Android

La integración Android usa Play Integrity Standard. AbuseShield implementa el
endpoint de nonce, la verificación servidor contra Google y un cliente Kotlin en
`clients/android/sdk`.

El cliente contiene dos fronteras explícitas:

- `MobileIntegrityProtocol` normaliza la solicitud, conserva una copia inmutable
  del body y construye el binding y el sobre v2
- `GooglePlayIntegrityTokenSource` adapta `StandardIntegrityTokenProvider` sin
  introducir otra biblioteca de red

La app sigue siendo responsable de pedir el nonce y enviar la solicitud final
con su cliente HTTP habitual.

## Antes de escribir código cliente

1. Habilita la API Play Integrity en tu proyecto de Google Cloud; si distribuyes
   por Play, vincula también la aplicación en Play Console
2. Crea una cuenta de servicio con el acceso mínimo necesario
3. Registra package name, operaciones, métodos y paths en el registro móvil firmado
4. Monta la credencial en `deploy/mobile-secrets/google-play-service-account.json`
5. Empieza en `shadow`; no uses el resultado para endurecer decisiones hasta observar dispositivos reales

Una app todavía no publicada puede probar el transporte y obtener un token,
pero una build instalada por ADB no demuestra que Play reconozca esa versión.
La protección HTTP del backend no requiere este proveedor. Consulta primero la
[guía Android](../guias/android.md) para distinguir ambos caminos.

Variables del runtime:

```dotenv
ABUSE_SHIELD_MOBILE_INTEGRITY=true
ABUSE_SHIELD_MOBILE_REGISTRY_MODE=shadow
ABUSE_SHIELD_MOBILE_REGISTRY_PATH=/run/abuseshield-mobile/mobile-registry.signed.json
ABUSE_SHIELD_MOBILE_REGISTRY_KEYRING_PATH=/run/abuseshield-mobile/keyring.json
ABUSE_SHIELD_MOBILE_PROVIDERS=play_integrity
ABUSE_SHIELD_GOOGLE_PLAY_INTEGRITY=true
ABUSE_SHIELD_GOOGLE_PLAY_CREDENTIALS_PATH=/run/abuseshield-mobile-secrets/google-play-service-account.json
```

El endpoint de decodificación de producción está limitado a HTTPS y `playintegrity.googleapis.com`. No apuntes esa variable a un proxy arbitrario.

El bearer OAuth permanece en memoria del proceso; no se publica en Redis.
Un lock compartido limita renovaciones simultáneas y devuelve
`play_integrity_oauth_refresh_busy` a los procesos que no pueden adquirirlo.
Esto limita la concurrencia, pero no elimina los intercambios OAuth de procesos
fríos ni conserva tokens entre solicitudes PHP-FPM. Sin Redis, la protección de
renovación queda limitada al proceso y no coordina otros workers.

## Flujo del cliente

1. Prepara `StandardIntegrityTokenProvider` con anticipación
2. Construye una sola representación final del request
3. Pide el nonce a AbuseShield
4. Calcula `provider_binding` con [la fórmula v2](mobile-protocol.md#fórmula-canónica)
5. Entrega ese hexadecimal a `setRequestHash(...)`
6. Solicita el token estándar
7. Envía el request y el sobre v2 con `attestation_provider=play_integrity`

La emisión del nonce es `POST application/json`; no coloques operación, app o ruta en el query. El sobre declara `proof_type=standard_integrity_token` y `proof_encoding=compact_token`.

La documentación oficial de Google describe la preparación del proveedor y el uso de `requestHash`: [Play Integrity Standard requests](https://developer.android.com/google/play/integrity/standard).

No pongas email, token de sesión u otro dato sensible directamente en `requestHash`; AbuseShield usa un SHA-256 hexadecimal del contexto completo.

## Construir el artefacto

```bash
cd clients/android
./gradlew --no-daemon \
  :sdk:testDebugUnitTest \
  :sdk:assembleRelease \
  :sdk:publishToMavenLocal
```

Coordenadas Maven: `io.abuseshield:abuseshield-android:0.1.7-internal`.
El harness de `device-harness` no declara permisos, no abre red y no solicita un
token. `scripts/verify-device.sh` lo instala, comprueba el vector en hardware,
ejecuta una carrera acotada, valida foreground/background y lo desinstala incluso
si falla.

`device-probe` es una app diagnóstica separada. En un Samsung A07 obtuvo un
nonce de 120 segundos, un token emitido por Google y un `GET /` protegido.
La traza del servidor confirmó la decodificación por `google-play-integrity-v1`:
el dispositivo cumplió `MEETS_DEVICE_INTEGRITY`, pero la APK instalada por ADB
dio `UNRECOGNIZED_VERSION`. El resultado observado fue `invalid`; el modo
`shadow` conservó `unverified` como estado efectivo y la acción HTTP fue
`allow`. Ninguno de esos dos últimos valores equivale a una app aprobada.
La app requiere `io.abuseshield.mobile.probe` y la operación `probe` para
`GET /` en el registro firmado; no se deja esa entrada habilitada por defecto.
[Construcción y uso del diagnóstico](../guias/android.md).

Si no dispones de distribución por Play, puedes ejecutar la [comprobación de
Android Key Attestation](../guias/android.md)
en un teléfono autorizado por ADB. Valida fuera del servidor una clave
atestada, la APK instalada, un reto fresco y el estado de arranque. No sustituye
Play Integrity ni cambia la decisión del runtime.

## Registro mínimo

La entrada de la aplicación debe declarar el proveedor y cada operación permitida:

```json
{
  "com.example.android": {
    "platform": "android",
    "provider": "play_integrity",
    "environment": "production",
    "credential_refs": ["android-prod"],
    "operations": {
      "login": {
        "methods": ["POST"],
        "paths": ["/v1/login"]
      }
    }
  }
}
```

Este fragmento pertenece dentro de `tenants.<tenant>.apps` de un registro completo firmado. `credential_refs` documenta y valida qué credencial administrada corresponde a la app. En esta versión el adaptador Play Integrity usa la ruta global montada en `ABUSE_SHIELD_GOOGLE_PLAY_CREDENTIALS_PATH`; el registro nunca contiene el JSON secreto.

## Resultados que debe manejar la app

| Respuesta | Conducta del cliente |
|---|---|
| `422` al pedir nonce | Corrige app, operación, método o path; no repitas sin cambios |
| `429` | Espera `Retry-After` |
| `503` | Conserva la operación y reintenta con backoff |
| Proveedor `unavailable` | Repite el mismo request si el nonce sigue vigente |
| Nonce busy | Evita dos envíos concurrentes de la misma operación |
| Challenge HTTP | Sigue el contrato de API; no abras una pantalla web por defecto |

## Verificar el lado servidor

```bash
vendor/bin/phpunit -c build/phpunit.xml \
  tests/PlayIntegrityProviderTest.php \
  tests/MobileIntegrityServiceTest.php \
  tests/RuntimeProductionReadinessGuardTest.php
```

El protocolo, el adaptador y la decodificación servidor se han ejecutado con
un token real desde Android. Todavía falta una build con package name y firma
reconocidos por Play, instalada por el canal previsto, antes de promover la
integridad de la app a `enforce`. La prueba ADB anterior no demuestra ese caso.
