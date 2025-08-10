# Respuestas HTTP

Tags: #HTTP #Web #PortSwigger 

```powershell  
	HTTP/2 200 OK
	Content-Type: text/html; charset=utf-8
	X-Frame-Options: SAMEORIGIN
	Content-Length: 3227

	<!DOCTYPE html>
	<html>
	    <head>
	        <link href=/resources/labheader/css/academyLabHeader.css rel=stylesheet>
	        <link href=/resources/css/labs.css rel=stylesheet>
	        <title>SQL injection vulnerability allowing login bypass</title>
	    </head>
	</html>
```

## Respuesta Headers comunes

| **Header**                      | **Descripción**                                                                                        |
| ------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Código de estado**            | Línea que indica si la solicitud fue exitosa o falló (ej. `200 OK`, `404 Not Found`).                  |
| **Server**                      | Indica el software y versión del servidor web (ej. Apache, nginx). Puede revelar información sensible. |
| **Set-Cookie**                  | Establece o actualiza cookies en el cliente (usadas para sesión o autenticación).                      |
| **Content-Type**                | Especifica el tipo de contenido devuelto (ej. `text/html`, `application/json`).                        |
| **Content-Length**              | Tamaño en bytes del cuerpo de la respuesta.                                                            |
| **Cache-Control**               | Controla cómo se almacena en caché la respuesta (`no-cache`, `max-age`, etc.).                         |
| **Content-Encoding**            | Indica si el contenido está comprimido (`gzip`, `br`, `deflate`, etc.).                                |
| **Access-Control-Allow-Origin** | Permite o restringe peticiones desde otros orígenes (política CORS).                                   |
| **Strict-Transport-Security**   | Obliga al navegador a usar HTTPS (previene ataques de downgrade).                                      |
| **X-Frame-Options**             | Evita que el contenido se embeba en iframes (prevención de clickjacking).                              |
| **X-Content-Type-Options**      | Previene que el navegador adivine el tipo de contenido (`nosniff`).                                    |
| **X-XSS-Protection**            | Activa protección básica contra XSS en navegadores antiguos.                                           |
| **ETag**                        | Identificador del recurso para control de versiones/caché.                                             |
| **Location**                    | Indica la nueva URL cuando hay redirecciones (`301`, `302`).                                           |
| **WWW-Authenticate**            | Usado para pedir credenciales al cliente en recursos protegidos (ej. `Basic realm`).                   |
| **Date**                        | Fecha y hora en que el servidor generó la respuesta.                                                   |
| **Connection**                  | Estado de la conexión TCP (`keep-alive`, `close`).                                                     |
| **Expires**                     | Fecha de expiración del contenido (usado con caché).                                                   |
## Códigos de estado comunes

|Código|Significado|
|---|---|
|200|OK (respuesta exitosa)|
|301|Redirección permanente|
|302|Redirección temporal|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor|
