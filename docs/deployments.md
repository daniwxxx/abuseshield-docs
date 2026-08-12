# Alcances de despliegue

Los nombres describen capacidades, no niveles de marketing. Cada alcance añade servicios y superficie operativa.

| Alcance | Incluye | Se usa para |
| --- | --- | --- |
| `core` | Entrada, aplicación, memoria compartida y control de arranque | Proteger tráfico con la superficie mínima |
| `operations` | `core` más consola autenticada, métricas y paneles | Operar y revisar decisiones |
| `intelligence` | `operations` más retención y correlación | Investigar evolución y campañas |
| `lab` | `intelligence` más pruebas y fallos controlados | Validar hipótesis en infraestructura propia |

Un alcance superior no arregla una configuración insegura. El mismo control de arranque se aplica antes de recibir tráfico.

## Regla de avance

Avanza solo cuando el alcance actual tiene:

- salud comprobada
- datos recientes de la entrada protegida
- un rollback reproducible
- una ruta clara para revisar falsos positivos

El laboratorio debe estar separado del tráfico humano y cada evidencia debe conservar su origen.
