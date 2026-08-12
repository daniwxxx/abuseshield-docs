# AbuseShield Docs

Documentación pública para entender, configurar y operar AbuseShield con una ruta clara de integración.

Aquí encontrarás la guía de arranque, los perfiles de despliegue, la configuración pública y el contrato HTTP de AbuseShield.

## Empezar

1. Lee [`docs/README.md`](docs/README.md)
2. Sigue [`docs/getting-started.md`](docs/getting-started.md)
3. Elige un alcance en [`docs/deployments.md`](docs/deployments.md)
4. Revisa [`config/abuseshield.env.example`](config/abuseshield.env.example)
5. Consulta [`docs/operation.md`](docs/operation.md) antes de abrir tráfico real

## Contenido

| Ruta | Para qué sirve |
| --- | --- |
| [`docs/getting-started.md`](docs/getting-started.md) | Primer arranque y comprobaciones |
| [`docs/deployments.md`](docs/deployments.md) | Alcances de despliegue |
| [`docs/configuration.md`](docs/configuration.md) | Variables públicas y límites |
| [`docs/architecture.md`](docs/architecture.md) | Fronteras y recorrido de una solicitud |
| [`docs/operation.md`](docs/operation.md) | Lectura diaria, degradación y vuelta atrás |
| [`docs/mobile-and-api.md`](docs/mobile-and-api.md) | Apps, WebView, API, NAT y redes cambiantes |
| [`api/abuseshield.openapi.yaml`](api/abuseshield.openapi.yaml) | Contrato HTTP público |
| [`config/abuseshield.env.example`](config/abuseshield.env.example) | Variables de ejemplo, sin valores reutilizables |

## Cómo usar esta documentación

La configuración de ejemplo muestra nombres, límites y valores seguros por defecto. La instalación completa se realiza con el paquete de distribución y la edición de despliegue que corresponda a tu entorno.

## Alcance de seguridad

Los valores de ejemplo que parecen secretos son marcadores deliberadamente inválidos. Deben generarse fuera del código y montarse mediante el mecanismo de secretos del entorno. No copies una clave de esta documentación a producción.

La consola, Grafana, Redis y ClickHouse se habilitan desde la edición de despliegue correspondiente y requieren sus propias credenciales.

## Estado

La documentación describe contratos públicos y decisiones de operación. No presenta métricas inventadas ni garantiza resultados de fraude sin una ventana de tráfico y evidencia verificable.

## Licencia

Consulta [`LICENSE`](LICENSE).
