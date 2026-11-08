# XInclude para leer archivos

Tags: #PortSwigger #XInclude 

* [XInclide attacks](https://github.com/sobinge/PayloadsAllThesobinge/blob/master/XXE%20Injection/README.md#xinclude-attacks)

```bash 
En est lab se explora una técnica alternativa a XXE basada en la inyección de XInclude. A diferencia de los laboratorios anteriores, aquí no podemos definir una DTD personalizada, ya que el XML del servidor está parcialmente predefinido y no nos permite insertar declaraciones externas.

Para sortear esa limitación, se aprovecha la funcionalidad XInclude, que permite incluir archivos externos dentro de documentos XML válidos. En este caso, inyectamos una etiqueta 'xi:include' con el espacio de nombres correspondiente y apuntamos al archivo /etc/passwd, usando 'parse="text"' para evitar errores de interpretación estructural.


- XInclude: Es un mecanismo estándar para componer documentos XML, escribiendo etiquetas de inclusión en el documento original, las que transcluirán otros documentos dentro de éste. XInclude puede incorporar tanto documentos XML como otros archivos de texto. 
```

```xml 
<!-- Petición original -->

	productId=1&storeID=1
```

```xml
<!-- Cuando no se puede modificar el DOCTYPE se puede usar el XIncluide --> 

	productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>&storeID=1
```