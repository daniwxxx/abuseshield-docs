# Elegir una edición

La edición determina qué servicios se levantan juntos. No cambia la forma de
instalar: desde la raíz del repositorio ejecuta `./abuseshield up <edición>`.

| Edición | Cuándo elegirla | Qué incorpora |
|---|---|---|
| `core` | Quieres conectar la protección y revisar el arranque desde la terminal | Entrada HTTP/HTTPS, aplicación, memoria compartida y comprobación de configuración |
| `operations` | Necesitas revisar solicitudes y el estado del servicio a diario | Lo anterior, War Room, gráficos, logs, trazas y procesos Go/Rust de comparación |
| `intelligence` | Necesitas conservar más eventos y relacionar actividad a lo largo del tiempo | Lo anterior, almacenamiento de eventos, análisis de conexiones y servicios adicionales de análisis |
| `lab` | Vas a probar cambios y provocar fallos en un entorno separado | Lo anterior y herramientas para pruebas de red y tráfico autorizado |

Los procesos Go/Rust de `operations` comparan resultados con PHP por defecto.
Su presencia no significa que se haya promovido toda la decisión a código nativo.
En `intelligence`, JA4 resume características de una conexión cifrada; no
identifica por sí solo a una persona. Si otra infraestructura termina la conexión
antes, la información disponible cambia.

## Carpetas y direcciones

| Carpeta desde la raíz del repositorio | Panel de operación | Grafana |
|---|---|---|
| `deploy/editions/core` | No se inicia | No se inicia |
| `deploy/editions/operations` | `http://127.0.0.1:8080/abuseshield/admin` | `http://127.0.0.1:3001` |
| `deploy/editions/intelligence` | La misma dirección | La misma dirección |
| `deploy/editions/lab` | La misma dirección | La misma dirección |

Todas reciben solicitudes en `127.0.0.1:8080` y ofrecen HTTPS en
`127.0.0.1:8443`. En `lab`, arrancar los servicios no lanza tráfico de ataque:
los generadores requieren una ejecución explícita.

## ¿Qué servidor necesito?

No hay una cifra medida de capacidad por edición que permita prometer cierto
número de usuarios o solicitudes por segundo. El consumo depende del tráfico,
las comprobaciones activas y cuánto historial conserves.

`core` es el punto de partida con menos servicios. `operations` mantiene además
varias bases de métricas y logs. `intelligence` añade almacenamiento y análisis;
`lab` necesita margen para la carga que tú generes. No elijas una máquina solo
por la memoria que usa el sistema cuando está quieto.

Después de arrancar, mide durante una prueba representativa:

```bash
docker compose stats --no-stream
docker system df
df -h .
```

Revisa también si las solicitudes se vuelven lentas y si algún contenedor se
reinicia por falta de memoria. La [guía de servidor](servidor.md) separa requisitos
del host, acceso remoto y publicación.

## Cambiar de edición

Cada edición tiene su propio proyecto y sus propios volúmenes. Detener `core`
y arrancar `operations` no traslada automáticamente su historial ni sus claves.
Tampoco pueden usar los mismos puertos a la vez. Antes de cambiar una instalación
que ya recibe tráfico, sigue [cambios y recuperación](cambios.md).
