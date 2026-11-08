# Bypass de autenticación JWT por confusión de algoritmo

Tags: #JWT #BurpSuite #Asimétrico #Simétrico 

```bash 
1. Descargar el 'JWT Editor' en Burpsuite
```

```bash 
En este lab, existe una implementación insegura de JWT que utiliza claves RSA para firmar y verificar tokens. Sin embargo, el servidor es vulnerable a un ataque de confusión de algoritmos: permite cambiar el algoritmo de firma a HS256, el cual utiliza una clave simétrica.

Se aprovecha que el servidor expone su clave pública en el endpoint '/jwks.json', y se usa como si fuera la clave secreta para firmar un nuevo token con HS256. Luego se modifica el payload para suplantar al usuario administrador y se firma el JWT con la clave pública, que el servidor acepta erróneamente.

Así se accede al panel de administración. Este lab demuestra cómo una mala configuración de los algoritmos aceptados en JWT puede anular por completo la seguridad del sistema.
```

```bash 
1. La llave pública del server a veces se expone a traves de un endpoint standard 

	/.well-known/jwks.json
	/jwks.json
	
❯ curl -s http://www.domain.com/jwks.json | jq       # Mirar la llave pública desde Kali 
```

## Crear un JWK con llave pública Asimétrico válido con 'JWT Editor' en Burpsuite 

```bash 
1. En algoritmos asimétricos se puede usar 'JWT Editor':
	- Dar click en 'New RSA Key'
	- Seleccionar 'JWK' y dar click en 'Generate'
	- Copiar la llave pública obtenida a traves del 'endpoint' y sustituir la anterior. Solo el contenido de 'kty, e, use, kid, alg y n' y dar click en 'ok'
	- Dar click derecho en el 'ID' y seleccionar 'Copy Public key as PEM'
	- El resultado pegarlo en el 'decoder', encodearlo a 'base64' y copiar el resultado para agregarlo a la 'symetric key'
```

## Crear un JWK Simétrico válido con 'JWT Editor' en Burpsuite 

```bash 
1. En algoritmos simétricos se puede usar 'JWT Editor':
	- Dar click en 'New Symetric Key'
	- Dar click en 'Generate'
	- Sustituir en la variable 'k' el resultado del 'Copy Public key as PEM' en 'base64' y dar click en 'ok'
```
## Crear un JWT válido 

```bash 
1. En la parte de 'JSON Web Token' del Proxy se modifica el 'Header' agregando lo siguiente debajo en 'alg':
   
   "alg": "HS256"

2. Modificar el campo del 'Payload':
   
    Header    .       Payload       .    Signature
    aaaaaa    .    administrator    .      cccccc
    
3. Para finalizar, se firma dando click en 'sign' seleccionando el JWK creado anteriormente y dando click en 'ok'
```