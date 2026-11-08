# Json Web Token 

Tags: #JWT

* [JWT](https://www.jwt.io/)
* [Token Dev](https://token.dev/)
* [Reverseshell](https://github.com/nicholasaleks/reverse-shells)

```bash 
El JWT es un mecanismo de autenticación

	- Cookie: Es un mecanismo de autenticación donde su contenido es aleatorio pero significa que le pertenece a el usuario que se autentico.
	- Token: Es un mecanismo de autenticación que contienen las siguientes propiedades.
		+ Estan firmados (Simétrica o Asimétrica)
		+ Se pueden decodear (Base64)
		+ Garantizar integridad y no confidencialidad 
		+ Expiran 

Los Token son transferidos hacia el cliente, quedan almacenados en ambos extremos (cliente, server) y todas las operaciones que se relicen despues de esa autenticación se envia el token. 
```

## Estructura de un Token JWT

```bash 

    Header        Payload        Signature
    aaaaaa    .    bbbbbb    .    cccccc


Header: Contiene los algoritmos de firma
	+ HMAC SHA (HS256, HS384, HS512) Simétrica por lo que es la misma llave para cifrar y verificar 
	+ RSA SHA (RS256)  Asimétrica por lo que se firma con la clave privada y se verifica con la calve pública 
	+ JKU -> Llave de firmado URL
	+ JWK -> Llave

Payload: Es el contenido o cuerpo 
	+ usuario: administrator 
	+ perfil: admin 
	+ password: 12345
	+ exp: 121225

Signature: Es la firma del token 
	+ Clave pública: Es utlizada para verificar 
	+ Clave privada: Es utilizada para firmar  
```

## Vulnerabilidades 

```bash 
- Exponen demasiada información 
- La validación de la firma: Es incorrecta o no la realizan 
- JWT firmados con alg. simétricos: Contraseña débil de firmado 
- JWT firmados con alg. asimétricos: Inyecciones en el HEADER: jwk, jku, kid 
- Ataques de algoritmos de CONFUSIÓN 
```