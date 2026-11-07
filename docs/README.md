# Guía de documentación

La documentación está ordenada por tarea y por nivel de detalle (`0.1.7`).

## Recorrido recomendado

1. [Cómo funciona](guias/como-funciona.md) y [Tu primera hora](guias/primera-hora.md) para entender el flujo y comprobar la primera solicitud
2. [Primer arranque](getting-started.md) para consultar la referencia abreviada de comandos
3. [Despliegues](deployments.md) y [Elegir una edición](guias/ediciones.md) para seleccionar una edición sin mezclar volúmenes
4. [Conectar una aplicación](guias/conectar-aplicacion.md) para integrar web, Laravel, API/WebView, Android o iOS
5. [Configuración](configuration.md) y [Configurar sin perder el camino](guias/configuracion.md) para validar secretos, red y límites
6. [Arquitectura](architecture.md) y [Canon de decisiones](reference/decision-canon.md) para entender las cuatro decisiones (`allow`, `observe`, `challenge`, `block`), el resultado operacional `retry` y los estados operativos (`degraded`, `quarantine`)
7. [Motores](engines.md) y [Señales y límites](guias/senales.md) para conocer qué señales se combinan y cuáles no bastan solas
8. [Operación](operation.md) y [Trabajo diario](guias/operar.md) para leer decisiones, hacer backups sellados y volver atrás
9. [Troubleshooting](troubleshooting.md) y [Resolver problemas](guias/problemas.md) cuando algo no responde como esperabas
10. [Compatibilidad](compatibility.md) y [Móvil y API](mobile-and-api.md) para clientes que no son un navegador convencional

## Referencias

- [Índice de guías paso a paso](guias/README.md)
- [Referencia técnica](reference/README.md)
- [Contratos de integración](integrations/README.md)
- [Configuración de ejemplo](../config/abuseshield.env.example)
- [Contrato HTTP OpenAPI](../api/abuseshield.openapi.yaml)
- [Seguridad para reportes](../SECURITY.md)
- [Cambios publicados](../CHANGELOG.md)

## Cómo leer una decisión

Busca siempre cinco cosas: acción (`allow`, `observe`, `challenge`, `block` o `retry` operacional), alcance, evidencia independiente disponible, dependencia que participó y ventana de tiempo. Una razón aislada no es una identidad ni una sentencia permanente.
