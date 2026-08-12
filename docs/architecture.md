# Arquitectura pública

## Recorrido

```text
cliente -> entrada protegida -> AbuseShield -> aplicación de origen
                         \
                          -> superficies de operación autenticadas
```

La entrada pública y la consola tienen propósitos distintos. La consola no debe convertirse en un camino alternativo hacia la aplicación.

## Qué se observa

AbuseShield combina contexto de conexión, capacidad del cliente, ruta, sesión, ritmo y evolución. La IP, una VPN, un navegador concreto o una red compartida no son una identidad suficiente por sí solos.

## Alcance de una respuesta

Una respuesta debe indicar si afecta a una solicitud, sesión, ruta, identidad vinculada o red compartida. Las redes de hoteles, oficinas, campus, operadores móviles y CGNAT requieren especial cuidado: muchas personas pueden compartir una salida pública.

## Degradación

Si una dependencia compartida no responde, el runtime debe marcar la coordinación como limitada. El fallback local no equivale a memoria compartida entre réplicas y no debe presentarse como si lo fuera.

## Privacidad

La telemetría pública debe usar identificadores derivados, límites de tamaño y retención definida. No se deben exportar IP, cookies, cuerpos, tokens ni mensajes de excepción crudos.
