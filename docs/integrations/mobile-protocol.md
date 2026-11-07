# Protocolo móvil v2

Esta página define el contrato que el servidor implementa. Android e iOS deben producir exactamente los mismos bytes antes de pedir la prueba al proveedor.

El registro firmado y el keyring público se cargan como archivos regulares,
sin enlaces simbólicos ni escritura para grupo u otros. Se comprueban identidad
y tamaño del archivo antes y después de leerlo mediante el mismo descriptor.
El UID del host puede diferir del usuario PHP del contenedor. Mantén el montaje
en solo lectura y su directorio bajo control del operador. La firma verifica el
contenido; las comprobaciones del archivo no sustituyen esa autenticación.

## Secuencia

1. El cliente decide método, path, query y body finales
2. Solicita un nonce para esa aplicación, operación, método y path
3. Calcula `request_digest` sobre la solicitud exacta
4. Calcula `provider_binding` con nonce y contexto de la operación
5. Pide a Play Integrity o App Attest una prueba ligada a `provider_binding`
6. Envía la solicitud original y el sobre v2 en `X-AbuseShield-Mobile-Integrity`
7. El servidor valida registro, binding, actor, nonce y prueba del proveedor

No cambies body, query, método o nonce entre los pasos 3 y 6.

## Pedir el nonce

```http
POST /abuseshield/mobile/nonce HTTP/1.1
Content-Type: application/json

{"operation":"login","app_id":"com.example.android","method":"POST","path":"/v1/login"}
```

El body admite como máximo 2 KiB por defecto. Un request desactivado, mal formado o fuera del registro no consume el presupuesto de emisión. El endpoint no acepta el tenant ni datos de identidad aportados por el cliente.

Respuesta:

```json
{
  "id": "0123456789abcdef0123456789abcdef",
  "nonce": "A_sample_nonce_value",
  "expires_in": 120,
  "operation": "login",
  "app_id": "com.example.android",
  "method": "POST",
  "path": "/v1/login"
}
```

El tenant no es un parámetro público. Lo selecciona el despliegue.

## Fórmula canónica

Todas las líneas usan UTF-8, un único byte LF (`0A`) como separador y ningún salto final.

```text
request_digest = hex_lower(sha256(
  UPPER_METHOD + "\n" +
  EXACT_REQUEST_TARGET + "\n" +
  EXACT_BODY_BYTES
))

provider_binding = hex_lower(sha256(
  "abuseshield.mobile-integrity.v2" + "\n" +
  normalized_operation + "\n" +
  app_id + "\n" +
  lowercase_nonce_id + "\n" +
  nonce + "\n" +
  request_digest
))
```

`EXACT_REQUEST_TARGET` empieza con `/` e incluye el query exactamente como será enviado. Por ejemplo `/v1/login?flow=mobile`. No incluye esquema, host ni fragmento. Un query reordenado produce otro binding.

El body son los bytes que viajan por HTTP, no un objeto JSON vuelto a serializar después. Para un request sin body se usa la secuencia vacía.

## Vector de prueba

Entrada:

```text
operation: login
app_id: com.example.android
nonce_id: 0123456789abcdef0123456789abcdef
nonce: A_sample_nonce_value
method: POST
target: /v1/login?flow=mobile
body: {"email":"a@example.test"}
```

Salida:

```text
request_digest: 52c67eed15b22893d843eed9ec97ae044d1c35d987359e5fc8fadea2ee4e2aff
provider_binding: d3b85d17d53c7a5a9ff887997e7c00e9e7c50819cc891729fe4c90ac0bdf4647
```

## Sobre HTTP

El valor del header puede ser JSON o JSON codificado como base64url sin padding. Base64url evita problemas con proxies y es la forma recomendada.

```json
{
  "schema": "abuseshield.mobile-integrity.v2",
  "nonce_id": "0123456789abcdef0123456789abcdef",
  "nonce": "A_sample_nonce_value",
  "platform": "android",
  "app_id": "com.example.android",
  "operation": "login",
  "method": "POST",
  "path": "/v1/login",
  "request_hash": "d3b85d17d53c7a5a9ff887997e7c00e9e7c50819cc891729fe4c90ac0bdf4647",
  "attestation_provider": "play_integrity",
  "proof_type": "standard_integrity_token",
  "proof_encoding": "compact_token",
  "proof": "opaque-provider-token"
}
```

Aunque el campo se llama `request_hash` por compatibilidad del contrato, su valor en v2 es `provider_binding`, no `request_digest`.

Valores permitidos:

| Plataforma | Proveedor | Tipo | Codificación | Key ID |
|---|---|---|---|---|
| `android` | `play_integrity` | `standard_integrity_token` | `compact_token` | no se envía |
| `ios` | `apple_app_attest` | `attestation` o `assertion` | `base64url` | obligatorio |

En iOS, `proof` contiene el objeto App Attest codificado en base64url y `key_id` identifica la clave creada por App Attest. Los 32 bytes obtenidos al decodificar el hexadecimal de `provider_binding` son el `clientDataHash` entregado a `attestKey` o `generateAssertion`; no uses los 64 caracteres ASCII ni vuelvas a aplicar SHA-256. DeviceCheck y `none` no son proveedores válidos en v2.

## Consumo y reintentos

El nonce pasa por `issued` → `reserved` → `committed`.

- Mientras una verificación conserva la lease, otra llamada recibe `mobile_nonce_busy`
- Un resultado terminal, válido o inválido, consume el nonce
- Una caída temporal marcada `retryable` libera la reserva
- El reintento debe usar el mismo nonce y el mismo request completo
- Cambiar query o body durante el reintento cambia el binding y se rechaza
- Una lease vencida puede recuperarse, siempre antes del vencimiento del nonce
- Redis ausente responde `unavailable` cuando el estado compartido es obligatorio

El cliente respeta `Retry-After` cuando existe y no solicita nonces en bucle. Los límites se aplican por actor, red y tenant.

## Verificación del contrato

```bash
vendor/bin/phpunit -c build/phpunit.xml \
  tests/MobileIntegrityServiceTest.php \
  tests/MobileIntegrityRegistryTest.php \
  tests/ProductionMobileRegistryValidatorTest.php
```

Estas pruebas validan el protocolo del servidor. No validan proveedores sobre hardware físico.
