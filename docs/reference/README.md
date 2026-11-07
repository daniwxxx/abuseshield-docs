# Referencia técnica

Esta sección organiza los contratos públicos que deben coincidir con la distribución ejecutable. Las [guías de uso](../guias/README.md) explican tareas completas; aquí se consulta autoridad, configuración, formato y comportamiento ante fallos.

## Elegir el nivel de detalle

| Tema | Explicación | Contrato técnico |
|---|---|---|
| Flujo y fronteras de confianza | [Cómo están conectadas las piezas](../guias/arquitectura.md) | [Arquitectura pública](../architecture.md) |
| Señales y decisiones | [Señales y límites](../guias/senales.md) | [Familias de motores](../engines.md) |
| Datos e identidad | [Privacidad y datos](../guias/privacidad.md) | [Arquitectura pública](../architecture.md) |
| Opciones y secretos | [Configurar sin perder el camino](../guias/configuracion.md) | [Configuración pública](../configuration.md) |
| Web y Laravel | [Conectar una web](../guias/web.md) | [Web](../integrations/web.md) y [Laravel](../integrations/laravel.md) |
| API, webhook y WebView | [Conectar un cliente limitado](../guias/api-webview.md) | [Capacidades limitadas](../integrations/webview-api.md) |
| Android | [Conectar Android](../guias/android.md) | [Play Integrity](../integrations/android.md) |
| iOS | [Conectar iOS](../guias/ios.md) | [App Attest](../integrations/ios.md) |
| Pruebas y límites de evidencia | [Probar una integración](../guias/probar.md) | [Compatibilidad por capacidad](../compatibility.md) |

## Sistema

| Pregunta | Documento |
|---|---|
| ¿Qué componente decide y qué dato puede creer? | [Arquitectura pública](../architecture.md) |
| ¿Cuáles son las cuatro decisiones y qué es `retry`? | [Canon de decisiones](decision-canon.md) |
| ¿Qué observa cada motor y cuáles son sus límites? | [Familias de motores](../engines.md) |
| ¿Qué opción cambia una decisión o un despliegue? | [Configuración pública](../configuration.md) |
| ¿Qué expone el contrato HTTP público? | [OpenAPI](../../api/abuseshield.openapi.yaml) |

## Integraciones

[Abrir el índice técnico de integraciones](../integrations/README.md)

| Cliente | Contrato |
|---|---|
| Edge no Laravel | [Web y WafKernel](../integrations/web.md) |
| Aplicación Laravel | [Middleware Laravel](../integrations/laravel.md) |
| Navegador | [Integración web](../integrations/web.md) |
| Android | [Play Integrity](../integrations/android.md) |
| iOS | [App Attest](../integrations/ios.md) |
| API, webhook o WebView | [Capacidades limitadas](../integrations/webview-api.md) |
| Sobre móvil | [Protocolo v2](../integrations/mobile-protocol.md) |

## Operación y seguridad

| Necesidad | Referencia |
|---|---|
| Interpretar una decisión o dependencia | [Operación](../operation.md) |
| Desplegar y separar proyectos | [Despliegues](../deployments.md) |
| Corregir un rechazo de arranque o fallo | [Troubleshooting](../troubleshooting.md) |
| Actualizar o volver atrás | [Cambios y recuperación](../guias/cambios.md) |
| Reportar un hallazgo de seguridad | [Seguridad](../../SECURITY.md) |
