# Despliegues

Las ediciones son alcances de operación. No son planes comerciales ni cambian las reglas de seguridad.

| Edición | Añade | Cuándo usarla |
| --- | --- | --- |
| `core` | Entrada protegida, aplicación, Redis y control de arranque | Primer tráfico y protección con superficie mínima |
| `operations` | Consola autenticada, métricas y paneles | Operación diaria y revisión de decisiones |
| `intelligence` | Retención, correlación y servicios de análisis | Investigación de evolución y campañas |
| `lab` | Pruebas aisladas, proxy y fallos controlados | Validación en infraestructura propia o autorizada |

Cada edición incluye la anterior. El laboratorio no debe compartir estado con tráfico humano.

## Qué se necesita antes de subir de alcance

- health comprobable
- datos recientes de la entrada protegida
- secretos separados y rotables
- vuelta atrás probada
- operador que sepa leer un falso positivo y una omisión

## Un proyecto por edición

Usa un nombre Compose y un conjunto de volúmenes distinto para cada edición. No levantes `core` y `lab` sobre la misma red esperando que el aislamiento ocurra por nombre de servicio.

El paquete de distribución contiene el Compose de cada edición. Este repositorio publica la configuración explicada, no reemplaza los archivos de despliegue ni genera secretos por ti.

## Arranque seguro

Si falta un secreto, hay un placeholder, la consola está abierta sin autenticación o los umbrales están invertidos, el arranque debe quedar en `BOOT_DENIED`. Corregir la causa es parte de la instalación; bajar el guard para continuar no lo es.
