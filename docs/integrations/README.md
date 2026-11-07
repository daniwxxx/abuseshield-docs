# Referencia técnica de integraciones

Esta carpeta contiene contratos para implementar y revisar una integración. Los
recorridos de instalación están en [Conectar una aplicación](../guias/conectar-aplicacion.md)

## Web y Laravel

| Superficie | Contrato |
|---|---|
| Aplicación Laravel | [Paquete, middleware y rutas](laravel.md) |
| Aplicación detrás del edge | [Nginx y WafKernel](web.md) |

## API, webhook y WebView

- [Capacidad del cliente y respuestas](webview-api.md)
- [Protocolo móvil v2](mobile-protocol.md), cuando la API pertenece a una app instalada

## Android

- [Contrato Play Integrity](android.md)
- [Protocolo móvil v2](mobile-protocol.md)
- [Registro móvil sin firma](mobile-registry.unsigned.example.json)

## iOS

- [Contrato App Attest y verificador delegado](ios.md)
- [Protocolo móvil v2](mobile-protocol.md)
- [Registro móvil sin firma](mobile-registry.unsigned.example.json)

## Autoridad

| Dato | Autoridad |
|---|---|
| Cuenta interna | Configuración del despliegue, nunca el request público |
| App y operaciones permitidas | Registro móvil firmado |
| Estado de nonce y replay | Redis compartido |
| Play Integrity | Respuesta decodificada por Google y validada por el servidor |
| App Attest | Verificador criptográfico autenticado operado por la instalación |
| Acción pública | `allow`, `observe`, `challenge` o `block` |

Una cabecera aportada por el cliente no crea confianza. Una prueba ausente tampoco
es una condena automática. La política utiliza únicamente resultados verificados
y conserva la indisponibilidad como estado operativo visible
