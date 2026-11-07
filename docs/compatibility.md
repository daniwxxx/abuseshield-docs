# Compatibilidad por capacidad

AbuseShield no trata a todos los clientes como un navegador moderno. La capacidad observada o declarada del cliente determina qué comprobaciones tienen sentido.

| Cliente | Lo que puede aportar | Respuesta compatible | Qué no hay que asumir |
| --- | --- | --- | --- |
| Navegador | Cookies, JavaScript, navegación y recursos | Prueba interactiva o PoW de navegador | Que una pausa, bloqueador o extensión sea abuso |
| API | Método, ruta, cabeceras, autenticación y ritmo | Respuesta estructurada y `Retry-After` temporal | Que deba resolver un reto visual HTML |
| Webhook | Firma, idempotencia, reintentos y origen declarado | Validación de firma y ventana de replay | Que un reintento legítimo sea una campaña |
| App nativa | Sobre móvil v2 y atestación (`play_integrity` / `apple_app_attest`) | Verificación del proveedor o modo `shadow` | Que el User-Agent pruebe integridad |
| WebView | Parte de las capacidades de un navegador + puente nativo | Transporte normal o puente nativo hacia la app | Que soporte cookies, JS o almacenamiento igual que un navegador de escritorio |
| Dispositivo limitado | HTTP básico, poco almacenamiento o JS ausente | Reintento temporal acotado | Que la falta de una señal sea una señal hostil |

## Redes compartidas

Una IP puede representar un hotel, oficina, campus, operador móvil, VPN o CGNAT. Esa observación reduce la confianza en la IP como identidad, en vez de convertirla en un bloqueo colectivo.

## Señales ausentes

Que una señal no esté disponible no demuestra que el cliente sea malicioso. Queda registrado como contexto limitado y se combina con la secuencia, el alcance y la evolución de la sesión.

## Qué significa compatible

Compatible quiere decir que existe una respuesta operable para esa capacidad ([Conectar un cliente limitado](guias/api-webview.md) y [WebView, API y clientes sin navegador](integrations/webview-api.md)). No quiere decir que todas las señales estén disponibles ni que cada plataforma haya sido certificada en todos sus modelos físicos.
