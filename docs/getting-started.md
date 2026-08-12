# Primer arranque

Esta guía sirve para entender la instalación antes de enviar tráfico real. No requiere conocer los motores internos.

## Antes de empezar

- Docker Compose disponible en el equipo donde se ejecutará el servicio
- Una red de entrada conocida, si existe un proxy o balanceador delante
- Secretos generados fuera del repositorio
- Una ruta de vuelta atrás documentada

## Secuencia

```bash
cp config/abuseshield.env.example .env
# Reemplaza todos los marcadores por secretos generados fuera del repositorio
docker compose config --quiet
docker compose up -d
docker compose ps
```

El arranque debe detenerse si encuentra una clave de ejemplo, una consola sin autenticación, métricas públicas o una confianza de proxy que no pueda demostrar. El error útil explica qué falló, por qué importa y cómo corregirlo.

## Primera comprobación

Comprueba la entrada protegida desde la red prevista. Una respuesta de challenge o de denegación puede ser correcta; un panel vacío no demuestra que la entrada esté funcionando.

```bash
curl -i --max-time 5 http://127.0.0.1:8080/
docker compose ps
docker compose logs --tail=80 app
```

No publiques el token de operador, cookies, cuerpos de requests ni archivos `.env` en tickets o capturas.
