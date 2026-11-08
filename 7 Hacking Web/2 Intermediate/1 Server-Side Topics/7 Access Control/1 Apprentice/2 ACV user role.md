# Vulnerabilidades de control de acceso 

Tags: #AccessControl #BurpSuite 

## Rol de usuario controlado por parámetro

```bash 
En este lab durante el proceso de login, se intercepta la respuesta que establece una cookie llamada 'Admin=false'.

Al modificarla a 'Admin=true', obtenemos privilegios de administrador y se accede al panel oculto en /admin.
```

## Modificación del rol en el perfil de usuario

```bash 
En este lab al editar nuestra dirección de correo desde la cuenta, se intercepta la petición y se añade el campo '"roleid":2' al cuerpo JSON.

Esto modifica nuestro rol a administrador, permitiéndonos acceder al panel /admin.
```

```
# Petición original 
	{
		"email" : "test@test.com"
	}
```

```
# Petición modificada  
	{
		"email" : "test@test.com",
		"roleid" : 2
	}
```