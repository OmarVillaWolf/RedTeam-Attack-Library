# Enumeración por respuestas sutilmente distintas

Tags: #GestionAutenticacion #BurpSuite 

```bash 
En ese lab se ve cómo identificar un nombre de usuario válido mediante pequeñas diferencias en los mensajes de error que devuelve el servidor, y posteriormente se realiza un ataque de fuerza bruta sobre su contraseña para acceder a su cuenta.

Todo el proceso se realiza desde Burp Intruder, apoyándonos en la función Grep – Extract para detectar variaciones mínimas en la respuesta que nos permitan distinguir usuarios existentes.

Una vez obtenido el usuario, se ataca el campo de contraseña hasta obtener una combinación válida que permita iniciar sesión y acceder a su panel.
```

```bash 
	'Grep – Extract' ayudará a encontrar la variación mínima 

1. Agregar un nuevo 'match' seleccionando 'Invalid username or password.' con el fin de al momento de iniciar el ataque, el intruder muestre una nueva columna con ese match. El mensaje del match dependerá de lo que muestre el servidor como mensaje de error 
2. Cuando se encuentra un usuario valido este tambien muestra el mensaje de 'Invalid username or password' pero sin el punto final
```