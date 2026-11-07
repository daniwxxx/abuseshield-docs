# WebView, API y clientes sin navegador

Una WebView no siempre tiene las mismas cookies, JavaScript o almacenamiento que un navegador completo. Una API, webhook o tarea de fondo puede no tener interfaz. AbuseShield debe recibir la capacidad real del cliente y elegir una respuesta que pueda completar.

## Regla de integración

No falsifiques User-Agent, headers móviles o soporte JavaScript para obtener una decisión más favorable. Esos datos son controlados por el cliente y no conceden confianza.

| Cliente | Integración recomendada |
|---|---|
| WebView dentro de una app nativa | Transporte HTTP normal; usa el puente nativo para obtener prueba Android/iOS cuando esté disponible |
| API de una app móvil | Sobre móvil v2 en operaciones registradas |
| API servidor a servidor | Autenticación propia del servicio y challenge no interactivo |
| Webhook | Firma del proveedor, timestamp y replay controlado |
| CLI o dispositivo limitado | Reintento temporal o comprobación fuera de banda |
| Crawler autorizado | Identidad y política explícitas; no excepción global por User-Agent |

## WebView con puente nativo

El código nativo conserva la autoridad sobre Play Integrity o App Attest. La WebView puede solicitar una operación al puente, pero no recibe credenciales del proveedor ni decide el tenant. El puente:

1. Lee método, path, query y body que se enviarán
2. Solicita nonce
3. Calcula el binding v2
4. Obtiene proof del proveedor nativo
5. Devuelve a la capa de red el sobre para una única solicitud

No guardes el proof o nonce en `localStorage`. No reutilices un sobre al navegar a otra ruta.

Cuando una integración privada necesita declarar capacidad sin prueba móvil, el proxy de confianza puede enviar ambos headers:

```http
X-AbuseShield-Client-Profile: constrained_device
X-AbuseShield-Client-Token: <secreto-montado-en-el-proxy>
```

El valor debe coincidir con `ABUSE_SHIELD_CLIENT_PROFILE_TOKEN` y tener al menos 32 caracteres. Una declaración sin token válido se ignora; no reduce riesgo ni crea identidad. No expongas ese secreto dentro de JavaScript, una WebView o una app distribuida.

## API sin prueba móvil

Si el cliente no puede producir una prueba móvil, omite `X-AbuseShield-Mobile-Integrity`. Un sobre inventado o con `attestation_provider=none` es inválido.

La ausencia queda como `missing` o `unsupported` según el perfil observado. La política puede observar, limitar o pedir un reintento compatible; no debe transformar incapacidad técnica en evidencia concluyente de abuso.

## Respuestas

- `allow`: procesa la respuesta normalmente
- `observe`: el request continúa; no hay acción especial del cliente
- `challenge`: sigue el método indicado por headers/body, no supongas HTML
- `block`: detén la operación y conserva correlation ID para soporte

La respuesta pública expone la acción y `X-Correlation-ID`. Score, razones y estado del runtime quedan en la consola de operador; una app no debe inferirlos por tiempos de respuesta.

Para `Accept: */*`, el cliente debe estar preparado para una respuesta de API y no depender de una página HTML. Conserva `Retry-After` cuando exista.

## Pruebas mínimas

Comprueba cada cliente con cookies habilitadas y bloqueadas, JavaScript ausente, cambio de red, background/foreground, timeout y repetición concurrente. En el cliente registra status, acción y correlation ID sin guardar tokens o bodies sensibles; las razones se correlacionan del lado del operador.

El runner de compatibilidad está descrito en [mobile-compatibility-runner.md](../compatibility.md). Un request HTTP correcto demuestra transporte; no demuestra integridad móvil.
