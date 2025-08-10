# Abuso de APIs

Tags: #APIs #OWASP #Explotacion #Postman 

```bash 
Una API es una pieza de códifo que permite a diferentes aplicaciones comunicarse entre sí y compartir información y funcionalidades. Una API es un intermediario entre dos sistemas, que permite que una aplicación se comunique con otra y pida datos o acciones específicas. 
```


Cuando hablamos del abuso de APIs, a lo que nos referimos es a la explotación de vulnerabilidades en las interfaces de programación de aplicaciones (**APIs**) que se utilizan para permitir la comunicación y el intercambio de datos entre diferentes aplicaciones y servicios en una red.

Un ejemplo sencillo de API podría ser la integración de Google Maps en una aplicación de transporte. Imagina que una aplicación de transporte necesita mostrar el mapa y la ruta a seguir para que los usuarios puedan ver la ubicación del vehículo y el camino que se va a seguir para llegar a su destino. En lugar de crear su propio mapa, la aplicación podría utilizar la API de Google Maps para mostrar el mapa en su aplicación.

En este ejemplo, la API de Google Maps proporciona una serie de funciones y protocolos que permiten a la aplicación de transporte comunicarse con los servidores de Google y acceder a los datos necesarios para mostrar el mapa y la ruta. La API de Google Maps también maneja la complejidad de mostrar el mapa y la ruta en diferentes dispositivos y navegadores, lo que permite a la aplicación de transporte centrarse en su funcionalidad principal.

Adicionalmente, una de las utilidades que vemos en esta clase es **Postman**. Postman es una herramienta muy popular utilizada para probar y depurar APIs. Con Postman, los desarrolladores pueden enviar solicitudes a diferentes endpoints y ver las respuestas para verificar que la API está funcionando correctamente. Sin embargo, los atacantes también pueden utilizar Postman para explorar los endpoints de una API en busca de vulnerabilidades y debilidades de seguridad.

Algunos endpoints de una API pueden aceptar diferentes métodos de solicitud, como GET, POST, PUT, DELETE, etc. Los atacantes pueden utilizar herramientas de fuzzing para enviar una gran cantidad de solicitudes a un endpoint en busca de vulnerabilidades. Por ejemplo, un atacante podría enviar solicitudes GET a un endpoint para enumerar todos los recursos disponibles, o enviar solicitudes POST para agregar o modificar datos.

## Vulnerabilidades comunes que se pueden explotar a través del abuso de APIs:

```bash 
-   'Inyección de SQL': los atacantes pueden enviar datos maliciosos en las solicitudes para intentar inyectar código SQL en la base de datos subyacente.
-   'Falsificación de solicitudes entre sitios (CSRF)': los atacantes pueden enviar solicitudes maliciosas a una API en nombre de un usuario autenticado para realizar acciones no autorizadas.
-   'Exposición de información confidencial': los atacantes pueden explorar los endpoints de una API para obtener información confidencial, como claves de API, contraseñas y nombres de usuario.
```


Para evitar el abuso de APIs, los desarrolladores deben asegurarse de que la API esté diseñada de manera segura y que se validen y autentiquen adecuadamente todas las solicitudes entrantes. También es importante utilizar cifrado y autenticación fuertes para proteger los datos que se transmiten a través de la API.

Los desarrolladores pueden utilizar herramientas como Postman para probar la API y detectar posibles vulnerabilidades antes de que sean explotadas por los atacantes.

A continuación, se proporciona el enlace al proyecto de Github que utilizamos para desplegar con Docker el laboratorio vulnerable donde poder practicar la enumeración de APIs:

-   **crAPI**: [https://github.com/OWASP/crAPI](https://github.com/OWASP/crAPI)

## Rutas Típicas 

```bash 
/docs    # Mostrar información de documentación de la API
```

## Web 

```bash 
Todo lo que haremos, se encontrara aqui:

En el 'Inspector', en la parte de 'Network' de la pagina web,  ahi encontramos peticiones de la API

1. En 'XHR' podemos ver las solicitudes de la API
2. En 'login' podemos ver lo que se esta tramitando (**Headers** = Peticion por POST a un endpoint, Tambien esta la cabecera Authorization en donde vemos el JWT de tipo Bearer, 'Request' = Estructura en Json donde se envia el correo y la passwd, **Response** = Token de Sesion y es un JWT)
3. En 'dashboard' podemos ver ('Headers' = Peticion por GET a un endpoint, 'Response' = Informacion del usuario)
4. 'JWT' = Es un Jason Web Tokens y su estructura basica es la siguiente: 'Header . Payload . Signature'   
```

## POSTMAN

* [Postman - Windows](https://www.postman.com/downloads/)
* [Pstman - Debian](https://techbear.co/install-postman-debian-linux/)
* [JWT](https://www.jwt.io/)

```bash 
Usar 'POSTMAN' para enumerar las peticiones:

	1. Crear una nueva colección en 'Collections'
	2. Crear una petición en 'New > HTTP Request'
	3. Colocar la url y su método 
	4. Si es POST colocar en 'Body', seleccionar 'Raw'
		- Colocar la representación {"email":"omar","password":"1232"} y el formato (JSON) para que se asigne correctamente la cabecera 'application/json' en el 'Content-Type'
	5. Dar click en 'Send' y 'Guardar'
```

```bash 
En POSTMAN se pueden crear variables para colocar los JWT 

1. Crear variable en 'Variables' y su formato sería:
	Variable       Initial Value      Current Value
	accessToken    --                 JWT 

2. En 'Authorization' se coloca el tipo de variable. Para este caso es 'Bearer Token' y se coloca la variable '{{accessToken}}'


Notas:
	1. Se coloca el valor del JWT 'Header . Payload . Signature' 
	2. Para futuras consultas la variable se colocara como cabecera llamada 'Autorization' siempre y cuando se guarde dentro del proyecto 
```

## Atacando la API

```bash 

```