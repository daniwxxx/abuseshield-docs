# Aplicación web detrás de Nginx

`WafKernel` permite evaluar una solicitud desde una aplicación PHP sin cargar
el paquete Laravel. Para otro backend, el operador debe implementar un servicio
de preflight completo y el reenvío al origen privado.

## Frontera

```text
Integración propia: Internet -> proxy público -> WafKernel::preflight() -> origen privado
Nginx incluido: Internet -> EdgePreflightGuard -> PHP-FPM del proyecto
```

El Nginx incluido consulta `public/preflight.php` mediante `auth_request`.
Ese archivo ejecuta `EdgePreflightGuard`, no `WafKernel::preflight()`, y recibe
una muestra del contenido de hasta 8192 bytes. No configura un origen externo.
La comprobación completa del contenido, incluida su vinculación con pruebas
móviles, debe ejecutarse sobre los bytes originales en el middleware o kernel.

La instalación debe mantener el preflight PHP, PHP-FPM, Redis y el origen en
redes privadas. Si el origen conserva otro puerto público, esa ruta evita la
revisión.

## Headers

Antes de reenviar, Nginx elimina cualquier header reservado enviado por Internet. Solo después añade sus propios datos internos. Esto aplica a forwarding, tenant, red y huellas TLS como `X-AbuseShield-JA4`.

En `WafKernel`, el método efectivo viene de `REQUEST_METHOD`, no de
`X-Original-Method`. El kernel genera su correlation ID. En el filtro edge,
Nginx aporta contexto por el canal interno; no confundas ambos contratos.

## Contrato de preflight

`WafKernel::preflight()` ejecuta middleware y termina telemetría en la misma llamada. `WafKernel::handle()` permite que el caller reemplace el `204` por la respuesta del origen y luego debe llamar `WafKernel::terminate($response)` una sola vez.

No uses `handle()` sin `terminate()` si necesitas trazas completas.

## Fallo controlado

Si el middleware de `WafKernel` lanza una excepción:

- rutas sensibles, incluidas variantes codificadas de `.env`, `.git` y secretos, se bloquean
- métodos de escritura reciben challenge
- lecturas no sensibles pueden continuar en modo degradado
- el log conserva una referencia derivada y tipo de excepción, no el mensaje crudo

El cliente recibe la acción y un identificador de correlación. El modo degradado, la causa y la referencia del error permanecen en telemetría y en las superficies autenticadas del operador.

## Verificación

### Sensor opcional y consentimiento

`public/js/abuseshield_sensor.js` no sustituye la evaluación del servidor y no
debe activarse antes de obtener consentimiento. `AbuseShieldSensor.grantConsent()`
activa la instancia de la pestaña actual; `revokeConsent()` elimina la concesión
persistida, detiene eventos y descarta buffers sin enviar un beacon final.
Las otras pestañas del mismo origen también se detienen al retirar o borrar ese
almacenamiento. Una concesión desde otra pestaña no las activa automáticamente.

El sensor vuelve a comprobar el consentimiento antes de recopilar, solicitar un
token y enviar datos. La retirada aborta la petición de token pendiente y una
respuesta tardía no reactiva el sensor. No puede retirar un beacon ya entregado
al navegador antes de la revocación; el tratamiento posterior requiere su propia
política de conservación y eliminación.

### Recorrido del servidor

```bash
vendor/bin/phpunit -c build/phpunit.xml tests/WafKernelPreflightTest.php
```

En el despliegue real, prueba además:

1. El origen no responde desde una red pública
2. Un header interno falsificado no llega intacto a PHP
3. Una ruta normal atraviesa preflight y origen
4. Una ruta sensible codificada se cierra durante fallo controlado
5. Cada request deja un único correlation ID y una única terminación
