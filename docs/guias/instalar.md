# Instalar y abrir el servicio

Necesitas una copia completa del repositorio en tu equipo. No copies solo el
archivo Compose: las ediciones usan archivos de otras carpetas para construir
y configurar sus servicios.

## Comprueba Docker

En una terminal:

```bash
docker compose version
docker info
```

Se requiere Docker con Compose 2.24.4 o posterior. Si `docker info` no responde,
resuelve primero el [acceso a Docker](problemas.md#docker-no-responde).
La primera construcción necesita Internet para descargar imágenes y paquetes.
PHP, Go y Rust se compilan o ejecutan dentro de sus contenedores; no necesitas
instalarlos en el equipo para usar el CLI ni Compose.

## Arranca una edición

Desde la **raíz del repositorio**, el camino soportado es el CLI:

```bash
./abuseshield up core
./abuseshield status core
```

Sustituye `core` por la edición elegida. El primer arranque puede tardar porque
construye las imágenes que falten. `up` fija el proyecto `abuseshield-<edición>`,
espera los health checks y ejecuta el doctor. Una salida correcta identifica la
edición, los endpoints locales y cada comprobación.

No hace falta crear `.env`. El servicio `edition-bootstrap` genera las claves,
las guarda en un volumen de Docker y termina con código 0. La aplicación valida
la configuración antes de atender solicitudes.

No uses solo `docker compose up` a mano salvo que sepas el proyecto y los
checks: omite doctor y el naming canónico. Si necesitas Compose en crudo (por
ejemplo sin Bash/jq), entra en `deploy/editions/core` y usa
`docker compose up --build --detach --wait --wait-timeout 900`, luego
`docker compose ps --all`. Es normal que `edition-bootstrap` figure como
`Exited (0)`.

## Abre la primera dirección

En el mismo equipo, abre <http://127.0.0.1:8080>. Para ver la respuesta completa:

```bash
curl -i http://127.0.0.1:8080/
```

Una respuesta 403 puede ser una decisión de protección. Una conexión rechazada
significa que no has llegado al servicio. Guarda el valor de `X-Correlation-ID`
cuando aparezca: permite localizar esa solicitud sin enviar tu contraseña ni
el contenido de la petición.

La dirección HTTPS local es <https://127.0.0.1:8443>. El certificado inicial es
autofirmado y el navegador mostrará una advertencia. Para una prueba de terminal
local puedes usar `curl -k -i https://127.0.0.1:8443/`; para visitantes hace falta
un certificado válido, como explica [la guía de servidor](servidor.md).

## Revisar y detener

Desde la raíz del repositorio:

```bash
./abuseshield logs core app
./abuseshield down core
```

`down` detiene y retira los contenedores, pero conserva sus volúmenes. El próximo
`./abuseshield up core` reutiliza las claves y el estado. No añadas `--volumes`
para un reinicio: esa opción borra los datos persistentes.

Los diagnósticos del CLI requieren Bash, curl, jq y Python 3 en el equipo.

Si actualizas una instalación creada con un nombre antiguo, conserva ese nombre
como explica [recuperar el proyecto existente](problemas.md#después-de-actualizar-parece-que-no-hay-historial).

Continúa con [ediciones](ediciones.md) para añadir paneles o con
[claves y acceso](claves.md) si vas a aportar credenciales propias.
