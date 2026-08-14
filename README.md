# AbuseShield

Documentación pública para poner AbuseShield delante de una aplicación, entender sus decisiones y operar los cambios sin adivinar.

AbuseShield no trata una IP, una VPN o un navegador como una identidad. Lee el contexto disponible, aplica una respuesta acotada y deja una explicación que se pueda revisar. Cuando falta información, la respuesta no se presenta como una certeza.

## Para empezar

| Si necesitas | Ve a |
| --- | --- |
| Levantar la primera edición | [Primer arranque](docs/getting-started.md) |
| Elegir los servicios necesarios | [Despliegues](docs/deployments.md) |
| Configurar secretos y límites | [Configuración](docs/configuration.md) |
| Entender el recorrido de una solicitud | [Arquitectura](docs/architecture.md) |
| Saber qué hace cada familia de motores | [Motores](docs/engines.md) |
| Revisar una decisión | [Operación](docs/operation.md) |
| Resolver un problema | [Troubleshooting](docs/troubleshooting.md) |
| Integrar una app o API | [Móvil y API](docs/mobile-and-api.md) |

## Qué encontrarás aquí

- procedimientos con comprobaciones y resultado esperado
- configuración pública con marcadores que no funcionan como secretos
- diagramas de la entrada, el runtime y la observabilidad
- diferencias entre navegador, API, webhook, app y WebView
- límites conocidos y casos que todavía requieren evidencia externa
- contrato HTTP público en [OpenAPI](api/abuseshield.openapi.yaml)

## Lo que esta documentación no promete

No hay métricas de fraude inventadas ni una promesa de cero falsos positivos. La eficacia depende del tráfico, la aplicación, la configuración y la ventana de observación. Un panel vacío significa que falta fuente o actividad clasificada, no que el abuso sea cero.

## Regla de seguridad

Una configuración insegura debe detener el arranque. No copies valores del ejemplo a producción, no publiques el token de operador y no abras la consola para facilitar una prueba.

## Estado de la referencia

Esta carpeta describe la superficie pública y la operación que puede verificarse desde una distribución. No incluye código interno, secretos, rutas privadas ni instrucciones para evadir controles.

## Licencia

Consulta [LICENSE](LICENSE).
