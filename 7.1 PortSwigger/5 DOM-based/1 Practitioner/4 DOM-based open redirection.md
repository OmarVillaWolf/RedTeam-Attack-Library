# Redirección abierta basada en el DOM

Tags: #XSS-DOM #JavaScript 

```bash 
En este lab se encuentra una vulnerabilidad de tipo open redirection basada en el DOM. El comportamiento vulnerable se produce al hacer clic en un enlace que evalúa un parámetro url directamente desde el fragmento del location.href sin realizar ninguna validación.

- Open Redirect: Es una vulnerabilidad que permite redirigir a los usuarios a sitios maliciosos a través de URLs manipuladas dentro de la aplicación. 

	https://example.com/?url= https://maliciousweb.com
	https://example.com/registration/?redirectUrl=
```

```javascript 
// Código donde se acontece la inyección 

<div class="is-linkback">
	<a href='#' onclick='returnUrl = /url=(https?:\/\/.+)/.exec(location); location.href = returnUrl ? returnUrl[1] : "/"'>Back to Blog</a>
</div>
```

```javascript 
// Se hace en la url

https:we.com/post?postId=5&url=https://maliciousweb.com/
```