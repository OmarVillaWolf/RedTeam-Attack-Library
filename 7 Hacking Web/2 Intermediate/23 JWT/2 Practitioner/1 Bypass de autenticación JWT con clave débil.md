# Bypass de autenticación JWT con clave débil

Tags: #JWT #BurpSuite #HashCat #Simétrico #JWK

* [JWT secrets list](https://github.com/wallarm/jwt-secrets)

```bash 
1. Descargar el 'JWT Editor' en Burpsuite
```

```bash 
En esta lab, se analiza una mala práctica común en la implementación de autenticación basada en JWT: el uso de claves de firma extremadamente débiles. Se inicia sesión con un usuario estándar, se intercepta el token y se somete a un proceso de fuerza bruta utilizando herramientas como Hashcat para descubrir la clave secreta, que resulta ser una palabra sencilla incluida en diccionarios comunes.

Una vez obtenida la clave, se usa para firmar un token modificado en el que suplantamos al usuario administrador. Al enviar este JWT correctamente firmado, se accede al panel de admin.

Este laboratorio evidencia los riesgos de usar secretos predecibles y destaca la importancia de emplear claves robustas en sistemas de autenticación basados en tokens.
```

## Ataque de diccionario a un JWT

```bash 
❯ hashcat -a 0 jwt.txt jwt.secrets.list   # Obtener el secreto del campo 'signature' en el JWT 

	# jwt.txt = JWT que se obtiene del login 
	# jwt.secrets.list = Diccionario de secretos 


Notas:
	1. Guardar el JWT en un archivo en Kali para despues hacer fuerza bruta
	2. Se debe de conocer el 'secret' en el campo 'Signature' para firmar y verificar tokens
	3. El secreto se debe de codificar en el 'Decoder' en 'base64' para despues agregarlo en el 'JWT Editor' y generar una firma válida (JWK)
```

## Crear un JWK Simétrico válido con 'JWT Editor' en Burpsuite 

```bash 
1. En algoritmos simétricos se puede usar 'JWT Editor':
	- Dar click en 'New Symetric Key'
	- Dar click en 'Generate'
	- Sustituir el secreto obtenido en base64 donde se encuentra la variable 'k'
```

## Crear un JWT válido 

```bash 
1. En la parte de 'JSON Web Token' del Proxy se modifica el 'Payload' agregando el usuario administrator
   
    Header    .       Payload       .    Signature
    aaaaaa    .    administrator    .      cccccc
    
2. Para firmar el JWT con el nuevo JWK se da click en 'Sign' y se procede a seleccionar el JWK antes creado en el 'JWT Editor' sin cambiar alguna otra opción 
```