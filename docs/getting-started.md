# Empezar

Para una primera instalación con explicaciones de cada paso, consulta [Tu primera hora](guias/primera-hora.md). Esta página conserva la referencia abreviada de comandos, acciones y comprobaciones para quien ya conoce el flujo.

## Elegir una edición

| Necesidad | Edición | Proyecto Compose |
|---|---|---|
| Proteger solicitudes y conservar estado compartido | `core` | `abuseshield-core` |
| Revisar decisiones, métricas, logs y trazas | `operations` | `abuseshield-operations` |
| Retener eventos y ejecutar análisis adicionales | `intelligence` | `abuseshield-intelligence` |
| Provocar fallos y tráfico autorizado en un entorno separado | `lab` | `abuseshield-lab` |

Empieza con `core` si solo necesitas proteger tráfico. Usa `operations` cuando una persona vaya a revisar decisiones. `intelligence` y `lab` añaden servicios, memoria y superficie operativa; no mejoran una instalación mal conectada.

## Primer arranque

Desde la raíz del repositorio:

```bash
./abuseshield up core
./abuseshield status core
```

Sustituye `core` por la edición elegida. El arranque genera claves en un volumen si no existen. `setup` es opcional: crea y valida un `.env` antes del primer despliegue cuando prefieres aportar configuración desde el host.

`up` usa el proyecto `abuseshield-<edición>`, espera las comprobaciones de salud y ejecuta el diagnóstico. Una salida correcta debe identificar la edición, las direcciones locales y cada comprobación. No uses solo `docker compose ps` como prueba de que la consola, TLS o el aislamiento funcionan.

## Verificar una solicitud

La dirección predeterminada es loopback:

```bash
curl -i http://127.0.0.1:8080/
curl -k -i https://127.0.0.1:8443/
```

Busca el estado HTTP y las cabeceras `X-AbuseShield-Action` y `X-Correlation-ID`. Un `403` puede ser una decisión de protección; una conexión rechazada, un tiempo agotado o un `502` apunta al despliegue. La puntuación, el estado operativo y los motivos se revisan desde la superficie autenticada del operador, no desde la respuesta pública.

## Acceso del operador

En `operations`, `intelligence` y `lab`:

```bash
./abuseshield access operations
```

El comando muestra las URLs. El panel visual reconoce el acceso desde el mismo host y no pide una clave. Usa `--show-secrets` únicamente cuando una automatización necesite llamar a la API administrativa:

```bash
./abuseshield access operations --show-secrets
```

`core` debe devolver `404` para `/abuseshield/admin`. Las demás ediciones abren el panel y sus datos desde loopback. Una petición remota sigue cerrada salvo que presente la credencial administrativa válida.

## Conectar el cliente correcto

- [Web detrás de Nginx](integrations/web.md)
- [Laravel](integrations/laravel.md)
- [Android](integrations/android.md)
- [iOS](integrations/ios.md)
- [WebView, API y webhooks](integrations/webview-api.md)

No declares una app móvil como navegador para obtener un challenge distinto. Describe su capacidad real y habilita integridad móvil solo cuando el registro y el verificador estén configurados.

## Decisiones y estados del servicio

Si la configuración de producción rompe una garantía de seguridad, el runtime se niega a arrancar (`BOOT_DENIED`; consulta [Troubleshooting](troubleshooting.md) y [Resolver problemas](guias/problemas.md)).

Las cuatro acciones públicas son `allow`, `observe`, `challenge` y `block` (más `retry` como resultado operacional cuando el estado compartido de challenge no está disponible; ver [Canon de decisiones](reference/decision-canon.md)). Un `403` puede corresponder a un bloqueo o a una comprobación no resuelta. `degraded` y `quarantine` describen el estado operativo del runtime o de un módulo; no son acciones públicas ni una acusación contra el cliente.

Para la revisión diaria de decisiones y vuelta atrás, consulta [Operación](operation.md) y [Trabajo diario](guias/operar.md).

## Detener

```bash
./abuseshield down core
```

Usa `--volumes` únicamente cuando quieras eliminar el estado de esa edición:

```bash
./abuseshield down core --volumes
```

El recorrido completo paso a paso está en [Tu primera hora](guias/primera-hora.md) y los errores de arranque en [Troubleshooting](troubleshooting.md).
