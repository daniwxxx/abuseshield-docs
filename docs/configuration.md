# Configuración pública

Esta lista describe el contrato operativo. Los nombres pueden variar según el adaptador de despliegue; la configuración efectiva siempre debe validarse antes de arrancar.

## Variables esenciales

| Variable | Uso | Regla |
| --- | --- | --- |
| `ABUSESHIELD_PRIVACY_KEY` | Derivar identificadores operativos | Única, larga y fuera del repositorio |
| `ABUSESHIELD_ADMIN_ENABLED` | Activar la consola | Desactivada si no existe una frontera autenticada |
| `ABUSESHIELD_ADMIN_TOKEN` | Autenticar operación | Nunca se comparte con testers |
| `ABUSESHIELD_METRICS_PUBLIC` | Exposición pública de métricas | Desactivada salvo protección explícita |
| `ABUSESHIELD_TRUST_CLIENT_HEADERS` | Confianza en cabeceras de transporte | Desactivada salvo edge conocido |
| `ABUSESHIELD_TRUSTED_PROXY_CIDRS` | Proxies que pueden aportar contexto | Lista explícita, nunca una red abierta |

## Umbrales

La progresión debe conservar el orden `observe < challenge < block`. Un despliegue debe rechazar valores ausentes, mal tipados, invertidos o fuera del rango permitido.

## Secretos

El archivo de ejemplo contiene marcadores inválidos a propósito. Genera valores nuevos con el mecanismo de secretos de tu plataforma. No uses `change-me`, `test`, el nombre del proyecto ni una contraseña personal.
