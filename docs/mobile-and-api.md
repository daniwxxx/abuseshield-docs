# Apps, API y redes cambiantes

## No todo cliente es un navegador

Una app nativa, WebView, webhook, API, crawler autorizado y navegador tienen capacidades diferentes. El challenge debe negociarse según esa capacidad; no se debe exigir JavaScript o cookies a un cliente que no puede usarlos.

## iOS y Android

La plataforma declarada por el cliente no es una prueba de integridad. Las pruebas fuertes deben venir de App Attest o Play Integrity y verificarse en servidor. Un cambio de Wi-Fi a datos móviles, Private Relay, VPN, roaming o NAT compartido cambia el contexto sin demostrar abuso.

La evidencia de laboratorio, Waydroid o una cabecera propia sirve para probar el contrato y los fallos. No equivale a una atestación válida de Apple o Google.

Durante una integración móvil comprueba por separado:

- app nativa y WebView
- cookies, almacenamiento y JavaScript disponibles
- cambio de red durante una sesión
- reintentos en segundo plano
- expiración y uso único de nonce
- respuesta cuando el proveedor de integridad no está disponible

## API

Una integración debe usar credenciales y límites propios, no imitar una sesión de navegador. Un webhook repetido puede ser un reintento legítimo; la idempotencia y el historial deben formar parte de la lectura.

## Integraciones autorizadas

Un crawler, tester o servicio interno debe declararse por credencial, ventana y alcance. La autorización no elimina los límites; permite separar una prueba conocida de tráfico público.
