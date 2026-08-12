# Apps, API y redes cambiantes

## No todo cliente es un navegador

Una app nativa, WebView, webhook, API, crawler autorizado y navegador tienen capacidades diferentes. El challenge debe negociarse según esa capacidad; no se debe exigir JavaScript o cookies a un cliente que no puede usarlos.

## iOS y Android

La plataforma declarada por el cliente no es una prueba de integridad. Las pruebas fuertes deben venir de los mecanismos de la plataforma y verificarse en servidor. Un cambio de Wi-Fi a datos móviles, Private Relay, VPN, roaming o NAT compartido cambia el contexto sin demostrar abuso.

## API

Una integración debe usar credenciales y límites propios, no imitar una sesión de navegador. Un webhook repetido puede ser un reintento legítimo; la idempotencia y el historial deben formar parte de la lectura.
