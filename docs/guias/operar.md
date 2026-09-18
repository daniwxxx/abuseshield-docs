# Trabajo diario

Desde la raíz del repositorio, este comando revisa el despliegue y sus accesos:

```bash
./abuseshield status operations
```

Cambia `operations` por tu edición. El diagnóstico comprueba contenedores,
respuestas HTTP y acceso autenticado; no se limita a ver si un proceso está vivo.

## Leer una decisión

| Acción | Qué ocurre |
|---|---|
| `allow` | La solicitud puede continuar |
| `observe` | Continúa y queda señalada para observación |
| `challenge` | Se pide una comprobación adicional |
| `block` | Se rechaza la solicitud |
| `retry` | El cliente debe esperar o reintentar según la respuesta |

`degraded` describe un estado de funcionamiento reducido, no otra forma de decir
que el visitante es hostil. Revisa qué dependencia falta y qué decisión se tomó.

En la War Room elige la cuenta y el período que corresponden. Busca la solicitud
por hora, ruta e identificador de correlación. Primero mira qué ocurrió y a qué
alcance se aplicó; luego revisa las razones y el estado de los servicios.

Grafana ayuda a ver la evolución de los datos. Un panel vacío puede significar
que no hubo tráfico, que elegiste otro período o que falló la recolección.
Consulta [la guía de paneles](../runbooks/dashboard-guide.md) para comprobar la
fuente antes de interpretar un cero.

## Cuando una persona no puede continuar

Pide la hora, la página que estaba usando, el tipo de dispositivo y el
identificador de la respuesta. No necesitas su contraseña ni sus cookies.

Por ejemplo, varias personas de un hotel pueden usar la misma dirección pública
de Internet. Si una recibió una comprobación, no concluyas que todas esas personas
son la misma ni bloquees esa dirección completa para resolver un caso individual.
Lo mismo ocurre en oficinas y algunas redes móviles.

Comprueba si la solicitud llegó a AbuseShield, qué acción recibió y si pudo
completar la comprobación. Una app sin navegador puede necesitar un mecanismo
distinto; una conexión lenta puede interrumpir el proceso. Corrige el alcance del
problema antes de cambiar la sensibilidad de todos los visitantes.

## Si Redis deja de responder

Redis guarda el estado compartido entre procesos. Para comprobarlo:

```bash
./abuseshield exec operations redis redis-cli PING
./abuseshield logs operations redis
```

`PONG` confirma que Redis responde ahora; no demuestra que todos los procesos
hayan recuperado su estado. Repite el diagnóstico y revisa las decisiones
posteriores. Si sigue el estado reducido, conserva el período del fallo y consulta
[resolver problemas](problemas.md) antes de reiniciar servicios a ciegas.
