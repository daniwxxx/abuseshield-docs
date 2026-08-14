# Compatibilidad por capacidad

AbuseShield no debe tratar a todos los clientes como un navegador moderno. La capacidad del cliente cambia qué comprobaciones tienen sentido.

| Cliente | Señales disponibles | Respuesta esperada |
| --- | --- | --- |
| Cliente | Lo que puede aportar | Qué no hay que asumir |
| --- | --- | --- |
| Navegador | Cookies, JavaScript, navegación y recursos | Que una pausa o una extensión sea abuso |
| API | Método, ruta, cabeceras, autenticación y ritmo | Que deba resolver un reto visual |
| Webhook | Firma, idempotencia, reintentos y origen declarado | Que un reintento sea una campaña |
| App nativa | Credencial de app y atestación si existe | Que el User-Agent pruebe integridad |
| WebView | Parte de las capacidades de un navegador | Que soporte cookies, JS o almacenamiento igual que Chrome |
| Dispositivo limitado | HTTP básico, poco almacenamiento o JS ausente | Que la falta de una señal sea una señal hostil |

## Redes compartidas

Una IP puede representar un hotel, oficina, campus, operador móvil, VPN o CGNAT. Esa observación debe reducir la confianza en la IP como identidad, no convertirla en un bloqueo colectivo.

## Señales ausentes

Que una señal no esté disponible no demuestra que el cliente sea malicioso. Debe quedar registrado como contexto limitado y combinarse con la secuencia, el alcance y la evolución de la sesión.

## Qué significa compatible

Compatible quiere decir que existe una respuesta operable para esa capacidad. No quiere decir que todas las señales estén disponibles ni que la plataforma haya sido certificada en todos sus modelos.
