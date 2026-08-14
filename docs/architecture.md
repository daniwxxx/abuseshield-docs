# Arquitectura pública

Esta vista explica el recorrido sin exponer nombres internos ni detalles que ayuden a esquivar controles.

## Recorrido de una solicitud

```mermaid
flowchart LR
    C[Cliente\nweb, app, API o webhook] --> E[Entrada protegida]
    E --> P[Prelectura\nforma, red y capacidad]
    P --> M[Runtime de decisión]
    M --> A{Respuesta}
    A -->|allow / observe| O[Aplicación de origen]
    A -->|challenge| H[Comprobación compatible]
    A -->|deny| D[Respuesta limitada]
    M --> T[Traza operativa sin datos crudos]
    T --> V[Consola y métricas protegidas]
```

La consola y la telemetría son salidas de operación. No son una segunda entrada hacia la aplicación.

## Qué ocurre dentro del runtime

```mermaid
flowchart TD
    R[Solicitud] --> N[Normalizar contexto]
    N --> I[Identidad vinculada y aislamiento]
    I --> S[Señales de conexión, ruta, sesión y ritmo]
    S --> G[Combinar evidencia independiente]
    G --> Q{¿La evidencia alcanza?}
    Q -->|no| L[Respuesta limitada y revisable]
    Q -->|sí| B[Política de acción]
    B --> X[allow, observe, challenge o deny]
    G --> H[Salud del runtime y módulos]
    H --> F[Degradación explícita]
    F --> X
```

## Fronteras que deben mantenerse

- La entrada pública es la única puerta hacia el origen
- La aplicación no confía en cabeceras de transporte que el cliente pueda escribir
- Los proxies confiables se declaran por red, no por una cabecera cualquiera
- La consola requiere autenticación y no se publica por defecto
- Redis coordina el estado compartido cuando está disponible; el estado local se marca como limitado
- La telemetría usa identificadores derivados, límites y retención definida

## Redes compartidas

Hoteles, oficinas, campus, operadores móviles, VPN y CGNAT pueden poner a muchas personas detrás de una misma dirección pública. Por eso una IP puede aportar contexto de red, pero no debe bastar para bloquear a todos.

## Qué no debe inferirse

Un fingerprint no identifica por sí solo a una persona. Un challenge superado no concede confianza permanente. Una señal ausente no prueba abuso. La decisión tiene que conservar alcance, motivo y nivel de evidencia.
