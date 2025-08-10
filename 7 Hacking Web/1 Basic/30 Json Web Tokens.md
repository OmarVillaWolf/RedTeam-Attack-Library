# Enumeración y explotación de Json Web Tokens (JWT)

Tags: #JWT #OWASP #Explotacion 

Los **Json Web Tokens** (**JWT**) son un tipo de token utilizado en la **autenticación** y **autorización** de usuarios en aplicaciones web. JWT es un estándar abierto (**RFC 7519**) que define un formato compacto y seguro para transmitir información entre diferentes partes de forma confiable.

En lo que respecta a la fase de enumeración y explotación de un JWT, esto se produce cuando un atacante es capaz de obtener información sobre los JWT que se utilizan en la aplicación, lo que podría permitir al atacante explotar las debilidades en la autenticación y autorización de la aplicación.

La enumeración de los JWT se produce cuando un atacante utiliza técnicas de fuerza bruta o cierta ingeniería inversa para obtener información sobre los JWT utilizados por la aplicación web. Por ejemplo, el atacante podría intentar adivinar los valores de los JWT mediante la construcción de **tokens falsos**, tratando de validar en todo momento si la aplicación web los acepta o no. Si el atacante tiene éxito en la enumeración de un JWT válido, podría obtener información confidencial, como nombres de usuario, contraseñas, roles de usuario y otros datos de autenticación y autorización.

La explotación de los JWT se produce cuando un atacante utiliza la información obtenida de la enumeración del JWT para explotar debilidades en la aplicación. Por ejemplo, si la aplicación web utiliza JWT para la autenticación, pero no valida adecuadamente la **firma** del JWT, un atacante podría falsificar el token y acceder a la aplicación web como si fuera un usuario legítimo.

Para prevenir la enumeración y explotación de los JWT, es importante utilizar prácticas seguras de desarrollo web, como la validación adecuada de las solicitudes de entrada, la gestión segura de las claves de firma JWT y la limitación del tiempo de expiración de los JWT.

A continuación, se os proporciona el enlace directo al proyecto de GitHub SKF-LABS, el cual estaremos usando para practicar la enumeración y explotación de los JWT:

-   **SKF-LABS**: [https://github.com/blabla1337/skf-labs](https://github.com/blabla1337/skf-labs)


## Json Web Token 

Para esta ocasión utilizaremos SKF Labs en donde tendremos esta interfaz para poder hacer uso de la vulnerabilidad de WebToken. Podemos ver que hay dos botones, el primero de 'Authenticate' es para que al momento de ser autenticados se nos genere un Json Web Token y el botón de 'Admin' es para que nos coloque un alias de usuario, en este caso guest. Además, para este ejemplo tenemos un JWT Null Cipher.

![](Pasted%20image%2020230524110032.png)

Podemos encontrar la Cookie de Sesión asignada en esta parte del Inspector.  Miramos que tenemos dos puntos en su estructura que divide a las tres partes:
* Parte inicial -> Header
* Parte central -> Payload (Puede contener propiedades que tengan información del usuario)
* Parte final -> Signature (Firma digital) esta se encarga de verificar la integridad del Token, esta se crea mediante un algoritmo de Hash criptográfico que utiliza por detrás una clave secreta.
Cabe mencionar que cada parte de los JWT estan en **base64**

![](Pasted%20image%2020230524110425.png)


* [Json-Web-Token](https://jwt.io/)

En esta pagina podemos ver el contenido del Json Web Token. 
Podemos observar en el Header: Algoritmo y tipo 
En el Payload: El id, iat y exp
Signature: Miramos que podemos colocar 256 bit secret y este al momento de colocarlo, hará que aunque modifiquemos los demás campos, nuestro JWT va a ser valido.

![](Pasted%20image%2020230524111138.png)

Para este tipo de vulnerabilidades donde tenemos un Null Cipher, muchas veces en las parte del Header podemos colocar en **"alg" : "None"** y con esto la parte del signature no seria necesario. 

Esto lo podríamos hacer desde consola para así obtener el JWT del usuario 2 con los datos del usuario 1.
```bash
❯ echo -n '{"alg":"NONE","typ":"JWT"}' | base64                            # Representaremos el Header en base64
❯ echo -n '{"id":2,"iat":1677770355,"exp":1677773955}' | base64            # Representaremos el Payload en base64
# Para el ultimo campo que es Signature, en este tipo de caso donde te acepta NONE, no es necesario colocar ese campo. 
```

El resultado obtenido lo colocamos de la siguiente manera, cabe mencionar que en el primer resultado del Header nos sale un '=' al final, por lo que se lo debemos de quitar.
![](Pasted%20image%2020230524113435.png)

Después lo cerramos y le damos al boton 'Admin', por lo que ahora somos el usuario 2. 


* Para el segundo laboratorio. 
Tenemos que la parte de Signature ahora si la validan.

![](Pasted%20image%2020230524113739.png)


![](Pasted%20image%2020230524114451.png)

Obtenemos el JWT del primer usuario. 

![](Pasted%20image%2020230524114556.png)


Si hacemos el procedimiento del primer lab, nos saldrá esto:
Que nos quiere decir que es necesario el campo Signature.

![](Pasted%20image%2020230524114821.png)


Por lo que debemos de conocer el 'Secreto' y colocarlo en el campo del signature. Para poder encontrar la palabra podemos hacer fuerza bruta o colocar palabras básicas. 

![](Pasted%20image%2020230524115806.png)


El nuevo JWT lo colocamos de esta manera. 
![](Pasted%20image%2020230524115925.png)

Y ahora podemos ser el usuario 2