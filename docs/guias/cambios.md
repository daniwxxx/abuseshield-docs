# Cambios y recuperación

Antes de actualizar, guarda qué versión funciona y cómo volver a ella. Las
imágenes, las claves y los datos deben corresponder entre sí.

## Preparar la copia

Desde la raíz del repositorio:

```bash
git status --short
git rev-parse HEAD
./abuseshield config operations
```

Si hay cambios locales, guarda también esos archivos: el identificador del
commit no los contiene. Conserva de forma privada el `.env`, los certificados,
la configuración montada desde el host y las credenciales de proveedores.

El script de backup rechaza la copia si detecta contenedores del proyecto en
marcha. Archiva volúmenes de Docker, no hace una copia coordinada de bases de
datos mientras escriben. Prepara una ventana de mantenimiento y detén
los servicios antes de archivarlos. Desde la carpeta de la edición:

```bash
docker compose stop
```

Después, desde la raíz:

```bash
bash scripts/edition-backup.sh operations
```

La carpeta de destino debe ser nueva; una copia anterior no se sobrescribe.
Mantén los servicios detenidos mientras se archivan y asegúrate de que ningún
otro proceso esté escribiendo en esos volúmenes.

Revisa el manifiesto: un volumen marcado `not_created` no está dentro de la copia.
El backup contiene secretos, no está cifrado y no incluye automáticamente los
archivos montados desde el host. Guárdalo fuera de la máquina que vas a actualizar,
en un almacenamiento privado, junto con los archivos anteriores.

## Probar la versión nueva

Ensaya primero en una instalación aparte, con otro nombre y otros puertos.
Elige la versión a desplegar y, desde la carpeta de su edición:

```bash
./abuseshield up operations
./abuseshield status operations
```

Comprueba una solicitud que llegue a tu aplicación, el acceso al panel y que el
estado se conserve. No cambies al mismo tiempo la versión y la sensibilidad de
las decisiones: después sería más difícil localizar la causa de una diferencia.

## Volver atrás

Recupera el código y las imágenes compatibles con la copia. El script de restore
requiere confirmación y reemplaza el contenido de los volúmenes seleccionados.
Solo úsalo con archivos propios y confiables, tras verificar la copia en un
entorno aparte. La ruta siguiente debe sustituirse por la de tu backup:

```bash
bash scripts/edition-restore.sh operations /ruta/privada/al/backup --confirm
./abuseshield up operations
./abuseshield status operations
```

La restauración no es una transacción: un error de disco o extracción puede
dejar una recuperación parcial. No deseches la copia original al primer arranque.

## Cambiar de edición o de servidor

Cambiar de `core` a `operations` crea por defecto otro conjunto de volúmenes.
Las claves y el historial no se trasladan por usar un nombre de edición distinto.
El restore comprueba la edición y no es una herramienta de conversión entre ellas.

Prueba el destino por separado y define qué estado conservar antes de mover
tráfico. La [política técnica de actualización](../upgrade-rollback-policy.md)
recoge los controles adicionales; no hay una migración universal de un comando
entre todas las combinaciones de edición, versión e infraestructura.
