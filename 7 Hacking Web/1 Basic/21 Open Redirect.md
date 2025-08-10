# Open Redirect

Tags: #OpenRedirect #OWASP #Explotacion 

La vulnerabilidad de redirección abierta, también conocida como **Open Redirect**, es una vulnerabilidad común en aplicaciones web que puede ser explotada por los atacantes para dirigir a los usuarios a sitios web maliciosos. Esta vulnerabilidad se produce cuando una aplicación web permite a los atacantes manipular la URL de una página de redireccionamiento para redirigir al usuario a un sitio web malicioso.

Por ejemplo, supongamos que una aplicación web utiliza un parámetro de redireccionamiento en una URL para dirigir al usuario a una página externa después de que se haya autenticado. Si esta URL no valida adecuadamente el parámetro de redireccionamiento y permite a los atacantes modificarlo, los atacantes pueden dirigir al usuario a un sitio web malicioso, en lugar del sitio web legítimo.

Un ejemplo de cómo los atacantes pueden explotar la vulnerabilidad de redirección abierta es mediante la creación de correos electrónicos de **phishing** que parecen legítimos, pero que en realidad contienen enlaces manipulados que redirigen a los usuarios a un sitio web malicioso. Los atacantes pueden utilizar técnicas de **ingeniería social** para convencer al usuario de que haga clic en el enlace, como ofrecer una oferta atractiva o una oportunidad única.

Para prevenir la vulnerabilidad de redirección abierta, es importante que los desarrolladores implementen medidas de seguridad adecuadas en su código, como la validación de las URL de redireccionamiento y la limitación de las opciones de redireccionamiento a sitios web legítimos. Los desarrolladores también pueden utilizar técnicas de codificación segura para evitar la manipulación de URL, como la codificación de caracteres especiales y la eliminación de caracteres no válidos.

A continuación, se proporcionan los enlaces a los 3 proyectos de Github que estaremos desplegando en esta clase para practicar esta vulnerabilidad en un entorno controlado, viendo diferentes casos e incluso técnicas para evadir posibles restricciones:

-   **Open Redirect 1**: [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection)
-   **Open Redirect 2**: [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection-harder](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection-harder)
-   **Open Redirect 3**: [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection-harder2](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection-harder2)


## Open Redirect 

1. URL-Redirection
El Open Re-Direct sucede cuando en la url puedes colocar otra url y en caso que te redirija a la pagina que colocaste, quiere decir que se esta suscitando uno. Este tipo de peticiones las hacemos por POST.

![0x420](Pasted%20image%2020230516133841.png)

Colocamos la url a la cual nos va a redirigir, en este caso es 'Google.es'

![](Pasted%20image%2020230516133752.png)

El que hace la petición en este tipo de casos es el servidor mismo y podríamos ocasionar un ataque de DDoS a otros servicios. 


2. URL-Redirection Harder
Para este lab tendremos una restricción en el punto de la url.
Capturamos los datos de la solicitud con BurpSuite y obtenemos lo siguiente.

![](Pasted%20image%2020230516135348.png)

Colocamos lo mismo que en el anterior ejemplo para ver si no redirige a Google.es. 

![](Pasted%20image%2020230516135708.png)

Pero en este caso no nos deja, ya que la respuesta nos coloca que no podemos colocar un punto en la redirección. 

![](Pasted%20image%2020230516135755.png)

Por lo que para poder evadir eso, podemos hacer lo siguiente y es 'url-encodear'. Esto se lograra subrayando el punto '.' después **Convert Selection > URL > URL-encode all characters**. Todo esto en BurpSuite. 

![](Pasted%20image%2020230516140044.png)

Después de 'url-encodearlo' y mandarlo, el servidor lo sigue detectando como un punto. Por lo que debemos de 'url-encodear' pero ahora el '%' y de esta manera podríamos evadir la restricción del punto y hacer que la redirección a la pagina de Google.es

![](Pasted%20image%2020230516140336.png)


3. URL-Redirection Harder 2
Para este lab tendremos una restricción en el punto de la url y la barra. 
Capturamos los datos de la solicitud con BurpSuite y obtenemos lo siguiente.

![](Pasted%20image%2020230516135708.png)

Podemos colocar lo ultimo de lab anterior y así poder burlar el punto.

![](Pasted%20image%2020230516140336.png)

Pero al momento de mandar la solicitud el servidor nos dice que no podemos usar las '/'. 

![](Pasted%20image%2020230516141059.png)

Por lo que deberíamos hacer lo siguiente para poder burlarlas.

Nota: Solo para HTTPS no es necesario colocar en la url las dos barras '//' y así podríamos burlar la parte de las barras. 

![](Pasted%20image%2020230516141344.png)

Los OpenRedirect los puedes utilizar para muchas cosas:
* DDoS
* Phishing 
* XSS