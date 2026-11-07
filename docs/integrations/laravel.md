# Laravel

El paquete registra configuración, bindings, rutas auxiliares, comandos Artisan y el alias de middleware `abuseshield`. No añade protección global automáticamente.

## Instalar desde una revisión fija

El paquete es interno y no está publicado en Packagist.

```bash
composer config repositories.abuseshield vcs git@github.com:daniwxxx/Abuseshield.git
COMMIT_SHA="<commit revisado de 40 caracteres>"
composer require "genesisif/abuseshield:dev-main#${COMMIT_SHA}" --with-all-dependencies
```

También puedes usar un checkout local:

```bash
composer config repositories.abuseshield path ../abuseshield
composer require genesisif/abuseshield:@dev --with-all-dependencies
```

Conserva `composer.lock` y registra el commit del checkout. Un repositorio `path` no es una fuente inmutable.

## Publicar configuración

```bash
php artisan vendor:publish --tag=abuseshield-config
php artisan vendor:publish --tag=abuseshield-migrations
php artisan migrate
php artisan abuseshield:status
```

Las migraciones se cargan automáticamente salvo que la configuración lo desactive. No publiques el panel o métricas sin middleware de autenticación.

## Aplicar middleware

Protege un grupo explícito:

```php
Route::middleware(['abuseshield'])->group(function (): void {
    Route::post('/login', LoginController::class);
    Route::post('/checkout', CheckoutController::class);
});
```

Si la aplicación debe estar protegida casi por completo, crea un grupo global de aplicación y enumera fuera de él únicamente health checks y callbacks que tengan su propia autenticación. Añade un test que liste las rutas sensibles y falle cuando alguna pierda el alias.

## Tenant

`abuseshield.tenant_id` es la autoridad para una instalación de tenant fijo. Si habilitas resolución por request, el manager exige tenant explícito para operaciones administrativas; no cae silenciosamente en `global`.

Nunca tomes el tenant de una query pública. Debe venir de host validado, credencial autenticada o contexto que el edge establezca y sanee.

## Desactivar

`ABUSE_SHIELD_ENABLED=false` debe hacer bypass antes de análisis, estado o proveedores. No lo uses como mecanismo de emergencia sin probar que el origen conserva sus propios límites y autenticación.

El guard de readiness no puede desactivarse en `production` o `prod`.

## Comprobar la integración

```bash
php artisan route:list
php artisan abuseshield:status
vendor/bin/phpunit -c build/phpunit.xml tests/AbuseShieldServiceProviderSecurityTest.php
```

La prueba de la aplicación debe incluir una ruta protegida y otra exclusión intencional. Confirma status, `X-AbuseShield-Action`, correlation ID y que el controlador solo se ejecuta cuando la acción permite continuar.
