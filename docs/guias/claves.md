# Claves y acceso

Para el primer arranque puedes dejar las claves sin definir. AbuseShield genera
valores distintos y los conserva en el volumen `edition_secrets` de tu proyecto.
Volver a arrancar no genera otro juego de claves.

## Entrar al panel

Después de arrancar `operations`, desde la raíz del repositorio:

```bash
./abuseshield access operations
```

El comando muestra las direcciones. Para mostrar las credenciales en una terminal
privada, sin grabación ni pantalla compartida:

```bash
./abuseshield access operations --show-secrets
```

Usa `ABUSE_SHIELD_ADMIN_TOKEN` en la War Room. Para Grafana, el usuario inicial
es `admin` y la contraseña es `GF_PASSWORD`. Sustituye `operations` si usas otra
edición con paneles. En `core` no hay consola habilitada por defecto.

Con Compose directo, desde la carpeta de la edición, puedes leer solo la clave
que necesitas:

```bash
docker compose exec -T app cat /run/abuseshield-secrets/ABUSE_SHIELD_ADMIN_TOKEN
```

## Aportar tus propias claves

Hazlo antes del primer arranque. En la carpeta de la edición, crea un `.env`
privado y añade únicamente las variables que vas a cambiar. No copies sin editar
`.env.example`: contiene valores de referencia que el arranque rechaza.

Para generar un valor puedes ejecutar:

```bash
openssl rand -hex 32
```

Coloca ese resultado después de `ABUSE_SHIELD_PRIVACY_KEY=` en el archivo.
Genera otro valor para cada clave adicional, guarda y restringe el acceso:

```bash
chmod 600 .env
./abuseshield up operations
```

Antes, `./abuseshield setup operations` desde la raíz prepara un `.env`
con claves generadas y valida su contenido. Esa preparación usa PHP y OpenSSL
del equipo; no es necesaria para el arranque automático con bootstrap.

| Nombre en la configuración | Para qué se usa |
|---|---|
| `ABUSE_SHIELD_PRIVACY_KEY` | Derivar identificadores usados en el estado y las decisiones |
| `ABUSE_SHIELD_ADMIN_TOKEN` | Entrar a las funciones de operación |
| `GF_PASSWORD` | Acceso inicial a Grafana |
| `IPC_SECRET` | Autenticar intercambios internos |
| `HONEYPOT_SECRET` | Proteger las señales de las rutas señuelo |
| `ABUSE_SHIELD_CLIENT_PROFILE_TOKEN` | Acreditar un perfil de cliente configurado por el operador |
| `DB_PASSWORD`, `CH_PASSWORD` | Acceso a los servicios de almacenamiento que los utilizan |

## Una clave nueva no equivale a una rotación

Si un volumen ya contiene una clave y aportas otra distinta, el arranque devuelve
`persisted_secret_conflict`. No reemplaza la clave guardada ni cambia contraseñas
en las bases de datos.

Para conservar la instalación, retira de tu `.env` la variable contradictoria
y vuelve a arrancar. Para rotar credenciales, prepara el cambio coordinado de los
servicios afectados y una copia recuperable. Borrar `edition_secrets` no es un
procedimiento de rotación.

No envíes `.env`, el contenido de los volúmenes ni el resultado de
`--show-secrets` en tickets. Las claves suministradas como variables pueden verse
en la configuración del contenedor de preparación por administradores de Docker;
ese acceso equivale a administrar el despliegue. Los backups también pueden
contenerlas y necesitan almacenamiento privado.

Las credenciales de Apple y Google se obtienen en sus plataformas y se configuran
aparte. El arranque no registra una app ni acredita dispositivos automáticamente:
consulta [Android](../integrations/android.md) o [iOS](../integrations/ios.md).
