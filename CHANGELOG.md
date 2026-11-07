# Changelog

## 0.1.7 - 2026-11-07

- Sincronizadas las 20 guías operativas en español (`docs/guias/`), incluyendo `como-funciona.md`, `primera-hora.md`, `conectar-aplicacion.md`, `web.md`, `api-webview.md`, `android.md`, `ios.md`, `arquitectura.md`, `senales.md`, `privacidad.md`, `seguridad.md`, `configuracion.md` y `probar.md`
- Incorporados los contratos técnicos de integración (`docs/integrations/`): web (`WafKernel` / `EdgePreflightGuard`), Laravel, API/WebView, Android (`play_integrity`), iOS (`apple_app_attest`), protocolo móvil v2 (`abuseshield.mobile-integrity.v2`) y ejemplo de registro móvil (`abuseshield.mobile-registry.v1`)
- Añadida la referencia técnica (`docs/reference/README.md`) y el canon de decisiones (`docs/reference/decision-canon.md`): cuatro decisiones públicas (`allow`, `observe`, `challenge`, `block`), `retry` como resultado operacional (`challenge_state_unavailable`) y separación explícita frente a estados operativos (`degraded`, `quarantine`)
- Actualizados `docs/getting-started.md`, `docs/architecture.md`, `docs/operation.md`, `docs/configuration.md`, `docs/compatibility.md`, `config/abuseshield.env.example` y `api/abuseshield.openapi.yaml` (`0.1.7`, endpoints `/abuseshield/clearance`, `/abuseshield/sensor-token` y `/abuseshield/mobile/nonce`), corrigiendo todos los enlaces internos y encabezados de tablas

## 0.1.2 - 2026-09-18

- Sincronizadas las primeras guías por tarea en `docs/guias/` y actualizado el uso de `./abuseshield up <edición>`

## 0.1.1 - 2026-08-14

- Reorganizada la entrada principal por tareas y no por mensajes comerciales
- Añadidos diagramas públicos de arquitectura, recorrido de decisión y familias de motores
- Ampliadas las guías de despliegue, configuración, operación y vuelta atrás
- Añadida una guía de troubleshooting con síntomas, comprobaciones y resultado esperado
- Aclarados los límites de compatibilidad para API, WebView, apps, redes compartidas e integridad móvil
- Documentada la diferencia entre contratos preparados y evidencia real de Apple o Google
- Alineados los nombres de variables públicas con el prefijo `ABUSE_SHIELD_`
- Publicado el contrato HTTP reducido y sin superficies administrativas
