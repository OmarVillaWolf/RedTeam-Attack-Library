# Enumeración de usuario por bloqueo de cuenta

Tags: #GestionAutenticacion #BurpSuite 

```bash 
En este lab se explota un fallo lógico en el sistema de bloqueo de cuentas para llevar a cabo una enumeración de usuarios. La aplicación bloquea temporalmente una cuenta tras varios intentos fallidos, pero la diferencia en los mensajes de error permite identificar qué nombres de usuario son válidos.

Se usa un ataque tipo 'cluster bomb' en Burp Intruder para enviar varias solicitudes por cada nombre de usuario, provocando que las cuentas válidas sean bloqueadas y devuelvan un mensaje de error distinto. Una vez identificado el usuario, se realiza un ataque de fuerza bruta contra su contraseña utilizando un ataque tipo 'sniper'. Tras esperar a que se levante el bloqueo temporal, se inicia sesión con las credenciales obtenidas.


Notas:
	1. Agregar una regex filtrando por 'Invalid username or password.' e iniciar el ataque en ambos casos 
	2. Deseleccionar 'URL-encode these characters'
```