# Troubleshooting

Empieza por el síntoma, comprueba una sola capa y conserva la salida. No cambies varias variables a la vez.

## El arranque queda rechazado

1. Lee el identificador de la regla
2. Comprueba si hay un placeholder o secreto repetido
3. Revisa el orden de umbrales y el proxy confiable
4. Corrige la causa y vuelve a ejecutar `docker compose config --quiet`

No desactives el production guard para continuar.

## La entrada devuelve 403 o challenge

Primero separa una decisión de protección de una caída del servicio:

```bash
curl -i --max-time 5 http://127.0.0.1:8080/
docker compose ps
docker compose logs --tail=80 app
```

Revisa acción, alcance, razón y dependencia. Una IP compartida, una VPN o un navegador endurecido requieren mirar la secuencia, no borrar una regla global.

## Redis no responde

Comprueba health, logs y el modo operativo. Durante la caída, varios workers no pueden compartir memoria solo por tener el mismo archivo de configuración. El runtime debe mostrar la degradación y recuperar autoridad compartida cuando Redis vuelva.

## La consola devuelve 404

En `core` puede ser correcto: la consola está desactivada. En `operations`, `intelligence` o `lab` verifica la ruta, el token, el puerto del host y el proyecto Compose utilizado. No publiques la consola para comprobar si existe.

## Grafana está sano pero no abre

Comprueba primero el puerto publicado y la dirección de escucha. Un servicio sano dentro de la red Compose no implica que sea accesible desde el host.

## Un cliente no puede completar el challenge

Confirma si es API, webhook, app, WebView o navegador. No fuerces una prueba visual a un cliente sin JavaScript o cookies. Revisa reloj, almacenamiento, cookies, límite de intentos y binding de sesión.

## Dos ediciones se mezclaron

Detén ambas, revisa nombres de proyecto, redes y volúmenes, y vuelve a levantarlas con identificadores separados. No borres estado antes de conservar una copia y confirmar cuál edición lo creó.

## Volver atrás

Guarda commit, imágenes, configuración efectiva y estado compatible. Detén la ampliación, restaura el conjunto conocido y comprueba health antes de recibir tráfico otra vez.

## Qué adjuntar en un reporte

- edición y versión
- hora y zona horaria
- cliente y capacidad disponible
- ruta y método, sin tokens ni cuerpo sensible
- acción observada
- correlation ID sanitizado
- comando ejecutado y resultado esperado
