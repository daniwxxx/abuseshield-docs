# Configuración pública

El archivo [`.env.example`](../config/abuseshield.env.example) muestra la superficie mínima. La distribución puede añadir variables según la edición, pero todas deben pasar la validación de arranque.

## Variables que casi siempre importan

| Variable | Qué controla | Valor seguro de referencia |
| --- | --- | --- |
| `ABUSE_SHIELD_PRIVACY_KEY` | Derivación de identificadores | Secreto único generado fuera del repositorio |
| `ABUSE_SHIELD_ADMIN_ENABLED` | Consola de operación | `false` hasta tener frontera y token |
| `ABUSE_SHIELD_ADMIN_TOKEN` | Acceso del operador | Secreto separado, largo y rotatable |
| `ABUSE_SHIELD_METRICS_PUBLIC` | Métricas accesibles desde Internet | `false` |
| `ABUSE_SHIELD_TRUST_CLIENT_HEADERS` | Confianza en contexto aportado por cabeceras | `false` |
| `ABUSE_SHIELD_TRUSTED_PROXY_CIDRS` | Redes que pueden aportar forwarding | Lista explícita |
| `ABUSE_SHIELD_OBSERVE_THRESHOLD` | Inicio de observación | Menor que challenge |
| `ABUSE_SHIELD_CHALLENGE_THRESHOLD` | Fricción adicional | Entre observe y block |
| `ABUSE_SHIELD_BLOCK_THRESHOLD` | Denegación | Mayor que challenge |

## Variables de operación avanzada

Estas opciones deben habilitarse solo con una razón y una ruta de vuelta atrás:

- `ABUSE_SHIELD_ADMIN_DEBUG_RAW`: material crudo de administración, nunca público
- `ABUSE_SHIELD_RAW_TELEMETRY`: salida sensible, normalmente desactivada
- `ABUSE_SHIELD_READ_LEGACY_IP_KEYED_STATE`: lectura temporal de estado antiguo
- `ABUSE_SHIELD_NATIVE_SHADOW`: comparación PHP/native sin cambiar enforcement
- `ABUSE_SHIELD_NATIVE_ENFORCE`: promoción nativa, solo después de equivalencia
- `ABUSE_SHIELD_MOBILE_INTEGRITY`: validación de plataforma móvil
- `ABUSE_SHIELD_ENGINE_SELF_HEALING`: recuperación de módulos con límites observables

El nombre de una variable no demuestra que la función esté lista para producción. La configuración efectiva y el health de sus dependencias forman parte de la comprobación.

## Secretos y archivos

Genera cada secreto fuera del repositorio y móntalo con el mecanismo de secretos del host. No uses `change-me`, `test`, el nombre del proyecto, una contraseña personal ni un valor copiado de este README.

Antes de arrancar verifica:

```bash
docker compose config --quiet
docker compose up -d
docker compose ps
```

Si `config` rechaza la combinación, conserva el mensaje completo sin secretos. El sistema debe explicar qué regla falló, por qué importa y cómo corregirla.

## Umbrales

La relación mínima es:

```text
observe < challenge < block
```

No cambies sensibilidad y versión al mismo tiempo. Un número más bajo no es automáticamente mejor: debe contrastarse con una ventana de tráfico y una cola de falsos positivos.
