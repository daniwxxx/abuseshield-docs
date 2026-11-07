# Configuración pública

El archivo [`.env.example`](../config/abuseshield.env.example) muestra la superficie mínima. La distribución añade variables según la edición, pero todas deben pasar la validación de arranque (`ProductionReadinessGuard`). Para una guía orientada a tareas, consulta [Configurar sin perder el camino](guias/configuracion.md) y [Claves y acceso](guias/claves.md).

## Variables principales

| Variable | Qué controla | Valor seguro de referencia |
| --- | --- | --- |
| `ABUSE_SHIELD_PRIVACY_KEY` | Derivación de identificadores por tenant | Secreto único generado fuera del repositorio |
| `ABUSE_SHIELD_ADMIN_ENABLED` | Consola de operación | `false` en `core`; `true` solo con frontera loopback y token |
| `ABUSE_SHIELD_ADMIN_TOKEN` | Acceso a la API administrativa | Secreto separado, largo y rotable |
| `ABUSE_SHIELD_METRICS_PUBLIC` | Métricas accesibles sin token | `false` |
| `ABUSE_SHIELD_TRUST_CLIENT_HEADERS` | Confianza en cabeceras de forwarding | `false` salvo detrás de proxy declarado |
| `ABUSE_SHIELD_TRUSTED_PROXY_CIDRS` / `ABUSE_SHIELD_PROXY_CIDRS` | Redes de proxies confiables | Lista CIDR explícita o vacía |
| `ABUSE_SHIELD_TRUST_EDGE_FINGERPRINT_HEADERS` | Confianza en fingerprints TLS del edge | `false` por defecto |
| `ABUSE_SHIELD_CHALLENGE_SECURE_COOKIE` | Atributo `Secure` de cookie de clearance | `true` en HTTPS |
| `ABUSE_SHIELD_OBSERVE_THRESHOLD` | Inicio de observación | Menor que `challenge` (`30`) |
| `ABUSE_SHIELD_CHALLENGE_THRESHOLD` | Fricción reversible | Entre `observe` y `block` (`60`) |
| `ABUSE_SHIELD_BLOCK_THRESHOLD` | Denegación acotada | Mayor que `challenge` (`90`) |

## Variables de operación avanzada

Estas opciones deben habilitarse solo con una razón verificada y una ruta de vuelta atrás:

- `ABUSE_SHIELD_ADMIN_DEBUG_RAW`: material crudo de administración, nunca público (`false`)
- `ABUSE_SHIELD_RAW_TELEMETRY`: salida sensible, desactivada en producción (`false`)
- `ABUSE_SHIELD_BACKUP_PASSPHRASE_FILE`: ruta al archivo de passphrase para backups sellados (`abuseshield-backup-v2`, PBKDF2-HMAC-SHA256 + AES-256-CBC + HMAC-SHA256)
- `ABUSE_SHIELD_NATIVE_SHADOW`: comparación PHP/nativo sin cambiar enforcement
- `ABUSE_SHIELD_NATIVE_ENFORCE`: promoción nativa, solo después de verificar equivalencia
- `ABUSE_SHIELD_MOBILE_INTEGRITY`: validación de protocolo móvil v2 (`false` hasta configurar registro firmado y keyring)
- `ABUSE_SHIELD_MOBILE_REGISTRY_MODE`: `shadow` durante observación inicial; `enforce` solo tras validar builds y proveedores reales

## Secretos y archivos

Genera cada secreto fuera del repositorio o mediante `./abuseshield up <edición>` / `./abuseshield setup <edición>`. No uses `change-me`, `test`, el nombre del proyecto, una contraseña personal ni un valor copiado de un ejemplo.

Antes de arrancar verifica:

```bash
./abuseshield config core
./abuseshield up core
./abuseshield status core
```

Si la validación rechaza la combinación (`BOOT_DENIED`), conserva el mensaje completo sin secretos. El sistema informa qué regla falló (`Reason`), qué riesgo evita (`Risk`) y cómo corregirla (`Fix`).

## Umbrales

La relación obligatoria es:

```text
observe < challenge < block
```

No cambies sensibilidad y versión al mismo tiempo. Contrasta cualquier ajuste con una ventana de tráfico y una revisión de falsos positivos.
