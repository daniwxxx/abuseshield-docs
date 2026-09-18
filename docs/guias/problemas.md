# Resolver problemas

Los comandos Compose de esta página se ejecutan desde la carpeta de tu edición.
Conserva el mensaje de error y su hora; antes de compartirlo, revisa que no incluya
credenciales.

## Docker no responde

```bash
docker info
```

`Cannot connect to the Docker daemon` indica que Docker está detenido o que
estás usando otro contexto. Comprueba `docker context show`. En un servidor
Linux con Docker administrado por systemd, revisa `systemctl status docker`.
En Docker Desktop, abre la aplicación. En una instalación rootless el servicio
pertenece a tu usuario, no necesariamente al sistema.

Si aparece `permission denied`, revisa el acceso de tu cuenta a Docker. No cambies
el socket a permisos abiertos para resolverlo. Si el servicio figura como
`masked`, averigua por qué se deshabilitó antes de volver a activarlo.

## El arranque muestra BOOT DENIED

```bash
docker compose logs --tail 100 edition-bootstrap app
```

El error incluye un código, la causa (`Reason`), el riesgo (`Risk`) y la
corrección (`Fix`). Usa el nombre de variable que aparece en ese mensaje.

| Mensaje o situación | Qué hacer |
|---|---|
| Clave insegura o `placeholder` en una instalación nueva | Retira ese valor para usar generación automática o reemplázalo por uno generado de forma criptográfica |
| `persisted_secret_conflict` | Hay una clave guardada distinta de la variable aportada; retira la variable contradictoria si quieres conservar la instalación |
| Debug sin protección, métricas públicas o frontera de proxy inválida | Corrige la opción indicada antes de arrancar, sin desactivar el validador |
| No se puede escribir el volumen | Comprueba espacio, permisos y si el sistema de archivos está en solo lectura |

Si la clave inválida ya está guardada en un volumen, no basta con editar `.env`.
Consulta [claves](claves.md) y conserva el estado antes de plantear una rotación.

## El puerto ya está ocupado

```bash
docker compose ps --all
ss -ltn
```

No detengas otro servicio sin identificarlo. Elige puertos distintos para la
nueva instalación como muestra [la guía de servidor](servidor.md). Comprueba la
dirección nueva al abrir el navegador.

## La página devuelve 403, 404 o 502

| Respuesta | Cómo distinguir el caso |
|---|---|
| `403` en una ruta de usuario | Revisa acción, hora e identificador: puede ser una decisión de protección |
| `404` en `/abuseshield/admin` usando `core` | Es lo esperado; para disponer del panel necesitas una edición que lo incluya |
| `401` o `403` al consultar datos del panel | Comprueba la clave y la cuenta seleccionada, no cambies las reglas de tráfico |
| `502`, timeout o conexión rechazada | Revisa los contenedores y los logs de Nginx y la aplicación |

```bash
docker compose logs --tail 80 nginx app
```

Una página de estado que responde no garantiza que tu aplicación esté conectada.
Prueba también una ruta que deba llegar a ella.

## El panel está vacío o Grafana no abre

Comprueba el período elegido, la dirección publicada y las credenciales. Si el
servicio está en un VPS, abre la conexión SSH de [acceso remoto](servidor.md):
los puertos iniciales no son públicos. Luego ejecuta `./abuseshield status` con
tu edición desde la raíz del repositorio y revisa
[las fuentes de datos](../runbooks/dashboard-guide.md).

## Una comprobación no termina

Anota dispositivo, navegador, ruta, hora y si cambiaba entre Wi-Fi y datos móviles.
No atribuyas el problema a un bot solo por usar VPN, bloqueadores o una dirección
compartida. Una API o una app puede no ejecutar JavaScript ni conservar cookies
como un navegador. Consulta [WebView y API](../integrations/webview-api.md) o
la integración móvil correspondiente.

## Después de actualizar parece que no hay historial

Comprueba primero el nombre del proyecto:

```bash
docker compose ls --all
```

Versiones anteriores de Compose directo podían crear un proyecto llamado
`core` u `operations`, en lugar de `abuseshield-core` o `abuseshield-operations`.
Los volúmenes de ese proyecto no se trasladan al nombre nuevo. Si ese era tu
despliegue, conserva su nombre explícitamente, por ejemplo
`docker compose -p operations ps --all`, y usa el mismo `-p` al arrancar o detener.
No borres volúmenes ni generes otras claves para intentar recuperar el historial.
