# Compatibilidad por capacidad

AbuseShield no debe tratar a todos los clientes como un navegador moderno. Esta tabla define qué puede afirmarse y qué debe negociarse.

| Cliente | Señales disponibles | Respuesta esperada |
| --- | --- | --- |
| Navegador | Cookies, JavaScript, navegación y recursos | Puede recibir challenge compatible |
| API | Método, ruta, cabeceras, autenticación y ritmo | Contrato HTTP, sin reto visual |
| Webhook | Firma, idempotencia, reintentos y origen declarado | Validación de integración |
| App nativa | Credencial de app y comprobantes de plataforma si existen | Binding de app, no suposición por User-Agent |
| WebView | Capacidades parciales de navegador y almacenamiento | Fricción negociada, nunca requisito universal |
| Dispositivo limitado | HTTP básico, poco almacenamiento o JavaScript ausente | Respuesta acotada y reintento compatible |

## Redes compartidas

Una IP puede representar un hotel, oficina, campus, operador móvil, VPN o CGNAT. Esa observación debe reducir la confianza en la IP como identidad, no convertirla en un bloqueo colectivo.

## Señales ausentes

Que una señal no esté disponible no demuestra que el cliente sea malicioso. Debe quedar registrado como contexto limitado y combinarse con la secuencia, el alcance y la evolución de la sesión.
