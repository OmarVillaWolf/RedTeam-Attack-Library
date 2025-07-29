# Abuso de APIs

Tags: #APIs #OWASP #Explotacion #Postman 

Cuando hablamos del abuso de APIs, a lo que nos referimos es a la explotación de vulnerabilidades en las interfaces de programación de aplicaciones (**APIs**) que se utilizan para permitir la comunicación y el intercambio de datos entre diferentes aplicaciones y servicios en una red.

Un ejemplo sencillo de API podría ser la integración de Google Maps en una aplicación de transporte. Imagina que una aplicación de transporte necesita mostrar el mapa y la ruta a seguir para que los usuarios puedan ver la ubicación del vehículo y el camino que se va a seguir para llegar a su destino. En lugar de crear su propio mapa, la aplicación podría utilizar la API de Google Maps para mostrar el mapa en su aplicación.

En este ejemplo, la API de Google Maps proporciona una serie de funciones y protocolos que permiten a la aplicación de transporte comunicarse con los servidores de Google y acceder a los datos necesarios para mostrar el mapa y la ruta. La API de Google Maps también maneja la complejidad de mostrar el mapa y la ruta en diferentes dispositivos y navegadores, lo que permite a la aplicación de transporte centrarse en su funcionalidad principal.

Adicionalmente, una de las utilidades que vemos en esta clase es **Postman**. Postman es una herramienta muy popular utilizada para probar y depurar APIs. Con Postman, los desarrolladores pueden enviar solicitudes a diferentes endpoints y ver las respuestas para verificar que la API está funcionando correctamente. Sin embargo, los atacantes también pueden utilizar Postman para explorar los endpoints de una API en busca de vulnerabilidades y debilidades de seguridad.

Algunos endpoints de una API pueden aceptar diferentes métodos de solicitud, como GET, POST, PUT, DELETE, etc. Los atacantes pueden utilizar herramientas de fuzzing para enviar una gran cantidad de solicitudes a un endpoint en busca de vulnerabilidades. Por ejemplo, un atacante podría enviar solicitudes GET a un endpoint para enumerar todos los recursos disponibles, o enviar solicitudes POST para agregar o modificar datos.

Algunas de las vulnerabilidades comunes que se pueden explotar a través del abuso de APIs incluyen:

-   **Inyección de SQL**: los atacantes pueden enviar datos maliciosos en las solicitudes para intentar inyectar código SQL en la base de datos subyacente.
-   **Falsificación de solicitudes entre sitios (CSRF)**: los atacantes pueden enviar solicitudes maliciosas a una API en nombre de un usuario autenticado para realizar acciones no autorizadas.
-   **Exposición de información confidencial**: los atacantes pueden explorar los endpoints de una API para obtener información confidencial, como claves de API, contraseñas y nombres de usuario.

Para evitar el abuso de APIs, los desarrolladores deben asegurarse de que la API esté diseñada de manera segura y que se validen y autentiquen adecuadamente todas las solicitudes entrantes. También es importante utilizar cifrado y autenticación fuertes para proteger los datos que se transmiten a través de la API.

Los desarrolladores pueden utilizar herramientas como Postman para probar la API y detectar posibles vulnerabilidades antes de que sean explotadas por los atacantes.

A continuación, se proporciona el enlace al proyecto de Github que utilizamos para desplegar con Docker el laboratorio vulnerable donde poder practicar la enumeración de APIs:

-   **crAPI**: [https://github.com/OWASP/crAPI](https://github.com/OWASP/crAPI)


## Web 

Todo lo que haremos, se encontrara aqui:

En el **'Inspector'**, en la parte de **Network** de la pagina web,  ahi encontramos peticiones de la **API**
* En **'XHR'** podemos ver las solicitudes de la API
	* En **login** podemos ver lo que se esta tramitando (**Headers** = Peticion por POST a un endpoint, Tambien esta la cabecera Authorization en donde vemos el JWT de tipo Bearer, **Request** = Estructura en Json donde se envia el correo y la passwd, **Response** = Token de Sesion y es un JWT)
	* En **dashboard** podemos ver (**Headers** = Peticion por GET a un endpoint, **Response** = Informacion del usuario)
* **JWT** = Es un Jason Web Tokens y su estructura basica es la siguiente: **Header | Payload | Signature**   

## POSTMAN

Usaremos **POSTMAN** para ir enumerando las peticiones.

0. Crearemos una nueva 'Collections'
1. Crearemos una petición 'HTTP Request'

![](Pasted%20image%2020230506141928.png)
Debemos de crear una **Collections** llamada crAPI en donde crearemos una nueva petición **HTTP Request** y ahí colocaremos lo que esta en la imagen y lo guardamos con el nombre de **Login**


2. Podemos trabajar con variables para que se haga mas fácil para las peticiones GET.

![](Pasted%20image%2020230506143646.png)

![](Pasted%20image%2020230506144302.png)

Usaremos el Token que nos ha otorgado el POSTMAN en la petición pasada de POST y lo salvamos.


3. Crearemos una nueva petición pero por el método GET, usando la variable que acabamos de crear con el Token.

![](Pasted%20image%2020230506144457.png)
Aquí ya no nos dará problemas, ya que ahora tendremos la autorización y esa nos saldrá automáticamente al momento de crearla por GET.

## Atacando la API

Aun faltaa................