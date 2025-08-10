# HTTP - Partes de una petición

Tags: #HTTP #Web #PortSwigger 

## Línea de solicitud

- Contiene el método, el endpoint (ruta) y la versión del protocolo.

```powershell  
	GET /filter?category=Pets HTTP/2
	Host: 0a1100db0429ca538243f2e8004d006a.web-security-academy.net
	Cookie: session=tb38FCAjPATfTNU6zEZBUOkvmqcDR9RA
	User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
	Connection: keep-alive
```

## Headers (cabeceras)

|**Header**|**Descripción**|
|---|---|
|**Host**|Indica el dominio de destino. Necesario cuando el servidor maneja múltiples sitios.|
|**User-Agent**|Información sobre el navegador, sistema operativo o herramienta que hace la petición.|
|**Cookie**|Envía cookies al servidor, útiles para mantener la sesión o autenticación.|
|**Referer**|URL de la página desde la cual se hizo la petición. Puede revelar rutas internas.|
|**Origin**|Indica el origen (protocolo + dominio) de donde se envió la petición. Importante para políticas CORS.|
|**Content-Type**|Tipo de dato enviado en el cuerpo (ej. `application/json`, `application/x-www-form-urlencoded`).|
|**Content-Length**|Indica el tamaño (en bytes) del contenido enviado en el body de la solicitud.|
|**Accept**|Define los tipos de respuesta que acepta el cliente (HTML, JSON, etc.).|
|**Accept-Encoding**|Especifica las codificaciones que acepta el cliente (gzip, deflate, br). Mejora la compresión de datos.|
|**Accept-Language**|Idiomas preferidos del cliente, por ejemplo `es-MX`, `en-US`.|
|**Authorization**|Token de autenticación (ej. `Bearer <JWT>`, `Basic <base64>`).|
|**X-Forwarded-For**|IP original del cliente cuando pasa por un proxy o balanceador. Útil para rastrear.|
|**Connection**|Indica si la conexión TCP debe mantenerse abierta (`keep-alive`) o cerrarse.|
## Headers de seguridad frecuentes 

|Header|Descripción|
|---|---|
|Origin|Indica el origen de la petición|
|Sec-Fetch-*|Encabezados modernos de navegación segura|
|Upgrade-Insecure-Requests|Solicita versión HTTPS si está disponible|

## Cuerpo (Solo en métodos como POST, PUT o PATCH)

```powershell 
	POST /login HTTP/1.1
	Host: 0a6b005004a5b83d80a03a0a009300c3.web-security-academy.net
	Cookie: session=JqhNAQWoORTHsGkPimCCLBoO3xiPzndP
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 67
	User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
	Connection: keep-alive
	
	username=admin&password=admin
```