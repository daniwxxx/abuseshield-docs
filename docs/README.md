# Guía

La documentación está ordenada por tarea, no por el nombre de un motor.

## Recorrido recomendado

1. [Primer arranque](getting-started.md) para preparar el host y comprobar el primer tráfico
2. [Despliegues](deployments.md) para elegir una edición sin mezclar volúmenes
3. [Configuración](configuration.md) para validar secretos, red y límites
4. [Arquitectura](architecture.md) para entender las fronteras
5. [Motores](engines.md) para conocer qué señales se combinan y cuáles no bastan solas
6. [Operación](operation.md) para leer decisiones y volver atrás
7. [Troubleshooting](troubleshooting.md) cuando algo no responde como esperabas
8. [Compatibilidad](compatibility.md) y [Móvil y API](mobile-and-api.md) para clientes que no son un navegador convencional

## Referencias

- [Configuración de ejemplo](../config/abuseshield.env.example)
- [Contrato HTTP](../api/abuseshield.openapi.yaml)
- [Seguridad para reportes](../SECURITY.md)
- [Cambios publicados](../CHANGELOG.md)

## Cómo leer una decisión

Busca siempre cinco cosas: acción, alcance, evidencia disponible, dependencia que participó y ventana de tiempo. Una razón aislada no es una identidad ni una sentencia permanente.
