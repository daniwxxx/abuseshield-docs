# AbuseShield

Documentación pública (`0.1.7`) para poner AbuseShield delante de una aplicación, entender sus cuatro decisiones públicas (`allow`, `observe`, `challenge`, `block` y el resultado operacional `retry`), integrar clientes web/API/Android/iOS y operar los cambios sin adivinar.

AbuseShield no trata una IP, una VPN o un navegador como una identidad humana. Lee el contexto disponible, aplica una respuesta acotada y deja una explicación verificable. Cuando falta información o cae una dependencia, la respuesta conserva la degradación visible sin inventar certezas.

## Para empezar

| Si necesitas | Ve a |
| --- | --- |
| Entender qué hace antes de instalarlo | [Cómo funciona](docs/guias/como-funciona.md) |
| Levantar y comprobar la primera edición paso a paso | [Tu primera hora](docs/guias/primera-hora.md) |
| Consultar comandos rápidos de arranque | [Primer arranque](docs/getting-started.md) |
| Elegir los servicios necesarios (`core`, `operations`, `intelligence`, `lab`) | [Despliegues](docs/deployments.md) y [Elegir una edición](docs/guias/ediciones.md) |
| Conectar una web, Laravel, API, WebView, Android o iOS | [Conectar una aplicación](docs/guias/conectar-aplicacion.md) y [Referencia de integraciones](docs/integrations/README.md) |
| Configurar secretos, fronteras de red y límites | [Configuración](docs/configuration.md) y [Claves y acceso](docs/guias/claves.md) |
| Entender el recorrido y el canon de decisiones | [Arquitectura](docs/architecture.md) y [Canon de decisiones](docs/reference/decision-canon.md) |
| Saber qué hace cada familia de motores | [Motores](docs/engines.md) y [Señales y límites](docs/guias/senales.md) |
| Revisar una decisión, hacer backups sellados o volver atrás | [Operación](docs/operation.md) y [Cambios y recuperación](docs/guias/cambios.md) |
| Resolver un problema de arranque o conectividad | [Troubleshooting](docs/troubleshooting.md) y [Resolver problemas](docs/guias/problemas.md) |

## Qué encontrarás aquí

- 20 guías operativas en español en [`docs/guias/`](docs/guias/README.md)
- Contratos técnicos de integración web, Laravel, API/WebView, Android (Play Integrity), iOS (App Attest) y protocolo móvil v2 en [`docs/integrations/`](docs/integrations/README.md)
- Referencia técnica y canon de decisiones en [`docs/reference/`](docs/reference/README.md)
- Configuración pública con marcadores que no funcionan como secretos en [`config/abuseshield.env.example`](config/abuseshield.env.example)
- Contrato HTTP público en [`api/abuseshield.openapi.yaml`](api/abuseshield.openapi.yaml)

## Lo que esta documentación no promete

No hay métricas de fraude inventadas ni una promesa de cero falsos positivos. La eficacia depende del tráfico, la aplicación, la configuración y la ventana de observación. Un panel vacío significa que falta fuente o actividad clasificada, no que el abuso sea cero.

## Regla de seguridad

Una configuración insegura detiene el arranque (`BOOT_DENIED`). No copies valores del ejemplo a producción, no publiques el token de operador y no abras la consola para facilitar una prueba.

## Licencia

Consulta [LICENSE](LICENSE).
