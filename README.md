# AbuseShield Docs

Documentación pública para entender, configurar y operar AbuseShield sin acceder al repositorio del runtime.

Este proyecto contiene únicamente documentación pública, ejemplos de configuración sin secretos y el contrato HTTP publicado. No contiene código de detección, datos de clientes, credenciales, paneles internos, resultados de pruebas ni artefactos de auditoría.

## Empezar

1. Lee [`docs/getting-started.md`](docs/getting-started.md)
2. Elige un perfil en [`docs/deployments.md`](docs/deployments.md)
3. Revisa [`config/abuseshield.env.example`](config/abuseshield.env.example)
4. Consulta [`docs/operation.md`](docs/operation.md) antes de abrir tráfico real

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

## Alcance de seguridad

Los valores de ejemplo que parecen secretos son marcadores deliberadamente inválidos. Deben generarse fuera del repositorio y montarse mediante el mecanismo de secretos del entorno. No copies una clave de este proyecto a producción.

Este repositorio no concede acceso al Command Center, Grafana, Redis, ClickHouse ni a ninguna superficie administrativa.

## Estado

La documentación describe contratos públicos y decisiones de operación. No presenta métricas inventadas ni garantiza resultados de fraude sin una ventana de tráfico y evidencia verificable.

## Licencia

Consulta [`LICENSE`](LICENSE).
