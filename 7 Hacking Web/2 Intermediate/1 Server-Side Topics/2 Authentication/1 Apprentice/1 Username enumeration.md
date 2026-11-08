# Enumeración de usuarios por respuestas distintas

Tags: #GestionAutenticacion #BurpSuite 


En este lab se trabaja con un fallo común en formularios de autenticación: respuestas distintas para usuarios inválidos y contraseñas incorrectas. Se aprovecha esta diferencia para enumerar usuarios válidos mediante Burp Intruder.

Una vez identificado un usuario real, se realiza fuerza bruta sobre su contraseña hasta conseguir acceso. Un ejemplo claro de cómo la inconsistencia en los mensajes de error compromete la seguridad del login.

NOTA:
	- En el login cuando un usuario es incorrecto se puede ver el siguiente mensaje: ``Invalid username`` y cuando el usuario es valido solo muestra: ``Incorrect password`` 

```bash 
Que observar en el 'Intruder' de Burpsuite:

1. Cuando la longitud 'Length' sea diferente a la de los demás indica un usuario válido.
2. Si se obtiene un usuario válido y se hace brute force a la password. La password correcta muestra un código de estado '302' el cual indica una redirección 
```