# SSRF básico contra sistema interno

Tags: #SSRF #PortSwigger 

```bash 
En este lab se explora un caso práctico de SSRF donde el servidor vulnerable interactúa con otro sistema interno que expone una interfaz de administración en la red privada. Aprovechamos la funcionalidad de verificación de stock para lanzar un escaneo controlado dentro del rango 192.168.0.X, variando la última parte de la IP.

Una vez identificamos qué IP responde en el puerto 8080 con una interfaz de administración accesible, manipulamos el parámetro vulnerable para interactuar con ella y ejecutar una petición que elimina al usuario carlos. Esta técnica demuestra cómo una SSRF aparentemente inocente puede convertirse en un vector para moverse lateralmente dentro de la infraestructura y comprometer otros servicios.
```

```bash 
# Petición original 
	stockApi=http://192.168.0.11:8080/product/stock/check?productID=1%26storeId=1
```

```bash 
# Petición modificada con el SSRF en el cual se debe de descubir el panel de administrador usando el 'Intruder' de BurpSuite para encontrar esa dirección 
	stockApi=http://192.168.0.1:8080

# una vez encontarda, se procede a borrar al usuario 
	stockApi=http://192.168.0.45:8080/admin
	stockApi=http://192.168.0.45:8080/admin/delete?username=carlos
```