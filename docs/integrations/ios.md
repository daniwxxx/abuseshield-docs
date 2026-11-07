# iOS

La integración iOS usa App Attest. AbuseShield gestiona nonce, replay, registro y política, pero delega la validación criptográfica de attestation y assertions a un servicio configurado por el operador. No existe SDK Swift ni verificador App Attest completo dentro de este repositorio.

## Componentes necesarios

- App con la capacidad App Attest habilitada
- Gestión de key ID y attestation en el backend
- Verificador que valide objetos App Attest según Apple
- Secreto HMAC compartido solo entre AbuseShield y ese verificador
- Registro móvil firmado con bundle ID, operaciones y rutas

Apple describe el challenge de un solo uso, la attestation inicial y las assertions posteriores en [Validating apps that connect to your server](https://developer.apple.com/documentation/devicecheck/validating-apps-that-connect-to-your-server).

## Configurar el runtime

```dotenv
ABUSE_SHIELD_MOBILE_INTEGRITY=true
ABUSE_SHIELD_MOBILE_REGISTRY_MODE=shadow
ABUSE_SHIELD_MOBILE_REGISTRY_PATH=/run/abuseshield-mobile/mobile-registry.signed.json
ABUSE_SHIELD_MOBILE_REGISTRY_KEYRING_PATH=/run/abuseshield-mobile/keyring.json
ABUSE_SHIELD_MOBILE_PROVIDERS=apple_app_attest
ABUSE_SHIELD_APPLE_ATTEST_VERIFIER_URL=https://verifier.internal.example/v1/verify
ABUSE_SHIELD_APPLE_ATTEST_SIGNING_SECRET_PATH=/run/abuseshield-mobile-secrets/apple-attest-signing-secret
```

El verificador debe estar en una red confiable, usar HTTPS y autenticar request y response con el contrato HMAC del adaptador. La referencia de tenant enviada es derivada; no se envía el tenant crudo.

`APP_DEBUG` no desactiva TLS. Para desarrollar un verificador en el mismo host,
la excepción `ABUSE_SHIELD_APPLE_ATTEST_ALLOW_LOCAL_HTTP=true` requiere además
`APP_ENV=local` o `testing` y debug activo. Solo permite HTTP a `127.0.0.1`, `::1`
o `localhost`; no sigue redirecciones ni usa el proxy del entorno para ese HTTP.
HTTPS sigue verificando el certificado también en desarrollo.

## Flujo de la app

1. Construye método, target con query y body exactos
2. Solicita un nonce a AbuseShield
3. Calcula `provider_binding` con [la fórmula v2](mobile-protocol.md#fórmula-canónica)
4. Decodifica los 64 caracteres hexadecimales del binding a 32 bytes y usa esos bytes como `clientDataHash`
5. Llama a `attestKey` para una clave nueva o `generateAssertion` para una clave ya registrada
6. Codifica el objeto App Attest en base64url sin padding
7. Envía `key_id`, `proof_type`, `proof_encoding=base64url` y `proof` en el sobre v2

El adaptador de AbuseShield no interpreta CBOR, certificados, contador o public key. Esas comprobaciones pertenecen al verificador delegado, que devuelve un resultado normalizado y firmado.

## Contrato del verificador

AbuseShield envía un JSON acotado con proveedor, plataforma, app, operación, nonce ID, `provider_binding`, `client_data_hash`, key ID, tipo, codificación, proof, momento de verificación y referencia derivada de tenant. `client_data_hash` es la codificación base64url de los 32 bytes del binding. El request incluye firma HMAC. El verificador debe:

- rechazar firma o timestamp inválidos
- comprobar app ID, entorno, key ID y challenge
- validar attestation o assertion completa
- persistir y comparar contador cuando corresponda
- limitar tamaño y tiempo de procesamiento
- devolver estado, `app_integrity` y `counter_state` explícitos, dimensiones conocidas y firma de respuesta

Una respuesta firmada pero sin `app_integrity=verified` o sin un contador aceptable (`initial`, `advanced` o `verified`) se rechaza. El adaptador actual usa un endpoint y secreto montados por despliegue; las referencias por app del registro son metadata validada, no un selector de secretos.

Un timeout, `429` o `5xx` es reintentable. Una firma, assertion o challenge inválido es terminal.

### Wire contract v1

La firma de request es `sha256=` más el HMAC-SHA256 hexadecimal de los bytes exactos del JSON recibido. No vuelvas a serializar el objeto antes de verificarla.

```http
POST /v1/verify HTTP/1.1
Content-Type: application/json
Accept: application/json
X-AbuseShield-Provider: apple_app_attest
X-AbuseShield-Provider-Signature: sha256=<hmac-del-body-exacto>
```

```json
{
  "schema": "abuseshield.app-attest-verification-request.v1",
  "provider": "apple_app_attest",
  "platform": "ios",
  "app_id": "com.example.ios",
  "operation": "login",
  "nonce_id": "0123456789abcdef0123456789abcdef",
  "tenant_ref": "0123456789abcdef0123456789abcdef",
  "provider_binding": "0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef",
  "client_data_hash": "ASNFZ4mrze8BI0VniavN7wEjRWeJq83vASNFZ4mrze8",
  "key_id": "app-attest-key-id",
  "proof_type": "assertion",
  "proof_encoding": "base64url",
  "proof": "opaque-app-attest-object",
  "issued_at": 1786032000
}
```

Una respuesta aceptable repite todos los campos de binding y declara el estado sin inferencias implícitas:

```json
{
  "provider": "apple_app_attest",
  "app_id": "com.example.ios",
  "operation": "login",
  "nonce_id": "0123456789abcdef0123456789abcdef",
  "tenant_ref": "0123456789abcdef0123456789abcdef",
  "provider_binding": "0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef",
  "status": "verified",
  "verified": true,
  "app_integrity": "verified",
  "device_integrity": "verified",
  "account_integrity": "unknown",
  "counter_state": "advanced"
}
```

El verificador firma los bytes exactos de esa respuesta en `X-AbuseShield-Provider-Signature`. AbuseShield rechaza una respuesta sin firma, con binding diferente, `app_integrity` distinto de `verified` o contador fuera de `initial`, `advanced` y `verified`.

## Casos que no deben confundirse con ataque

- App Attest no soportado por el dispositivo
- Primera attestation aún no completada
- reinstalación que necesita nueva key
- cambio Wi-Fi a red móvil durante la operación
- app en background y nonce vencido
- fallo temporal del verificador

Estos casos pierden evidencia o solicitan otra comprobación; no justifican un bloqueo por sí solos.

## Verificar el lado servidor

```bash
vendor/bin/phpunit -c build/phpunit.xml \
  tests/AppleAppAttestProviderTest.php \
  tests/MobileIntegrityServiceTest.php \
  tests/RuntimeProductionReadinessGuardTest.php
```

Hace falta una prueba adicional en iPhone o iPad físico con el entitlement, entorno y servidor reales. El repositorio no contiene esa evidencia.
