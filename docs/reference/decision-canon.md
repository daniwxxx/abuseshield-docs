# Canon de decisiones AbuseShield

Fuente canónica única para las cuatro decisiones de seguridad y para `retry` como resultado operacional. Versión descrita: `0.1.7`.

## 1. Las cuatro decisiones de seguridad

Son exactamente cuatro: `allow`, `observe`, `challenge`, `block`.

| Decisión | Efecto observable | Reversibilidad | Evidencia que la autoriza |
|---|---|---|---|
| `allow` | La solicitud continúa sin intervención | No aplica (no hay fricción) | Sin presión suficiente para intervenir |
| `observe` | Continúa sin prueba adicional; se conserva evidencia para comparar | Reversible (no añade fricción) | Señales que conviene conservar con razones, módulos y modo |
| `challenge` | Debe completar una comprobación compatible con el cliente o reintentar con espera | Reversible y acotada a actor, tenant y tiempo | La evidencia justifica fricción reversible negociada según capacidad observada |
| `block` | La solicitud no continúa, dentro de un alcance concreto (actor, ruta, operación, período, tenant) | Irreversible en el acto | Exige evidencia fuerte, independiente y con alcance; una señal repetida no se vuelve independiente por repetición |

## 2. `retry` es resultado operacional, no quinta decisión

`retry` significa: «repite el mismo request tras la espera indicada; no hay veredicto de seguridad en este acto». Es reversible por definición y nunca condena al visitante ni declara riesgo cero.

Cuando el estado compartido de challenge no está disponible (`challenge_state_unavailable`) o ocurre una condición transitoria de preflight, el runtime responde con `X-AbuseShield-Action: retry` y `Retry-After` sin publicar `block` ni `challenge` como veredicto del producto.

Regla: ningún panel, métrica o guía debe contar `retry` como `block` o `challenge`, ni como prueba de ataque, ni como absolución. `retry` se cuenta aparte con su `Retry-After` y su causa operativa.

`degraded` y `quarantine` tampoco son decisiones sobre el visitante: describen estado del servicio o de un módulo ([Cómo funciona](../guias/como-funciona.md); [Arquitectura pública](../architecture.md)). Una dependencia caída es capacidad reducida, no persona culpable ni riesgo cero.

## 3. Estado móvil canónico

> Android con SDK; iOS con adaptador de servidor disponible y cliente Swift pendiente.

- [Conectar Android](../guias/android.md): servidor y cliente Kotlin implementados; validación final de Google pendiente; APK instalada por ADB marcada como versión no reconocida, sin autorizar `enforce`.
- [Conectar iOS](../guias/ios.md): adaptador servidor y contrato autenticado implementados; sin biblioteca Swift ni verificador completo en el repositorio.
