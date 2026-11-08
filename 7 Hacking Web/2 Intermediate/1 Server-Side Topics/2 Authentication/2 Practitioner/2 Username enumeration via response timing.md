# Enumeración de usuario por tiempo de respuesta

Tags: #GestionAutenticacion #BurpSuite 

* [IP Bypass HTTP Header](https://gist.github.com/kaimi-/6b3c99538dce9e3d29ad647b325007c1)

```bash 
En este lab se explota una vulnerabilidad basada en el tiempo de respuesta del servidor para identificar nombres de usuario válidos. A través de Burp Suite, se lanza un ataque tipo timing aprovechando que el servidor tarda más en responder cuando el nombre de usuario es correcto pero la contraseña es incorrecta y larga.

Además, el laboratorio implementa un sistema de bloqueo por IP tras varios intentos fallidos, lo cual se evita manipulando la cabecera 'X-Forwarded-For' para simular distintas direcciones IP. Una vez identificado un usuario válido, se realiza un ataque por diccionario contra su contraseña hasta obtener acceso a la cuenta.


- IP block header bypass: Son posibles cabeceras que ayudan a bypasear el bloqueo despues de muchos intentos fallidos en los login
- X-Forwarded-For: Es un encabezado HTTP que contiene la dirección IP del cliente que realizó la solicitud. Se utiliza principalmente cuando el servidor web está detrás de un proxy o balanceador de carga. 
```

```bash 
# Petición original 
	POST /login HTTP/2
	
	username=omar&password=123
```

```bash 
# Petición modificada con la cabecera 'X-Forwarded-For' para simular distintas direcciones IP
	POST /login HTTP/2
	X-Forwarded-For: 127.0.0.1
	
	username=omar&password=123
	
	
Notas:
	1. Si se coloca un usuario válido pero una password no válida pero muy larga el servidor tiene a tardar mas que cuando se colocan credenciales invalidas.
	2. Se usa 'Intruder' con el ataque tipo 'Pitchfork' ya que usan múltiples payloads de manera paralela. El primero se usa variando el último número de la cabecera agregada y el segundo variando el usuario  
	3. El usuario correcto es el que tarde mas tiempo en 'Response completed'
```