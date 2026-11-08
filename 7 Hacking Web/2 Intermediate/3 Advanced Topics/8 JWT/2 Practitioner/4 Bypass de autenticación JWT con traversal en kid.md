# Bypass de autenticación JWT con traversal en kid

Tags: #JWT #BurpSuite #KID #Simétrico 

```bash 
1. Descargar el 'JWT Editor' en Burpsuite
```

```bash 
En este lab, hay una implementación insegura de JWT donde el servidor utiliza el valor del parámetro kid para leer desde el sistema de archivos la clave con la que validar la firma del token. Se aprovecha esto introduciendo una secuencia de path traversal en 'kid' que apunta al archivo '/dev/null', el cual tiene contenido predecible.

Firmamos nuestro JWT con una clave basada en un byte nulo y se modifica el payload para suplantar al usuario administrador. El servidor usa '/dev/null' como clave de verificación sin realizar validaciones adecuadas, por lo que acepta el token y nos concede acceso al panel de administración.

Este lab demuestra cómo una gestión insegura del parámetro 'kid' puede comprometer completamente el control de autenticación.
```

```python
# Ejemplo de la función:
   
   with open("keys/" + header["kid"], "rb") as f:
	   key = f.read() 
```

## Crear un JWK Simétrico válido con 'JWT Editor' en Burpsuite 

```bash 
1. En algoritmos simétricos se puede usar 'JWT Editor':
	- Dar click en 'New Symetric Key'
	- Dar click en 'Generate'
	- Sustituir un 'null byte' en base64 donde se encuentra la variable 'k' que es igual a 'AA=='
```

## Crear un JWT válido 

```bash 
1. En la parte de 'JSON Web Token' del Proxy se modifica el 'Header' agregando lo siguiente debajo en 'kid':
   "kid": "../../../../../../../dev/null",


2. Modificar el campo del 'Payload':
   
    Header    .       Payload       .    Signature
    aaaaaa    .    administrator    .      cccccc
    
3. Para finalizar, se firma dando click en 'sign' seleccionando el JWK creado anteriormente y dando click en 'ok'
```