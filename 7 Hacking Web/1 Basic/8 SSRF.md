# Server-Side Request Forgery (SSRF)

Tags: #SSRF #OWASP #Explotacion 

El **Server-Side Request Forgery** (**SSRF**) es una vulnerabilidad de seguridad en la que un atacante puede forzar a un servidor web para que realice solicitudes HTTP en su nombre.

En un ataque de SSRF, el atacante utiliza una entrada del usuario, como una URL o un campo de formulario, para enviar una solicitud HTTP a un servidor web. El atacante manipula la solicitud para que se dirija a un servidor vulnerable o a una red interna a la que el servidor web tiene acceso.

El ataque de SSRF puede permitir al atacante acceder a información confidencial, como contraseñas, claves de API y otros datos sensibles, y también puede llegar a permitir al atacante (en función del escenario) ejecutar comandos en el servidor web o en otros servidores en la red interna.

Una de las **diferencias** clave entre el **SSRF** y el **CSRF** es que el SSRF se ejecuta en el servidor web en lugar del navegador del usuario. El atacante **no necesita engañar a un usuario legítimo** para hacer clic en un enlace malicioso, ya que puede enviar la solicitud HTTP directamente al servidor web desde una fuente externa.

Para prevenir los ataques de SSRF, es importante que los desarrolladores de aplicaciones web validen y filtren adecuadamente la entrada del usuario y limiten el acceso del servidor web a los recursos de la red interna. Además, los servidores web deben ser configurados para limitar el acceso a los recursos sensibles y restringir el acceso de los usuarios no autorizados.

En esta clase, estaremos utilizando **Docker** para crear **redes personalizadas** en las que podremos simular un escenario de **red interna**. En esta red interna, intentaremos a través del SSRF apuntar a un recurso existente que no es accesible externamente, lo que nos permitirá explorar y comprender mejor la explotación de esta vulnerabilidad.

Para crear una nueva red en Docker, podemos utilizar el siguiente comando:

➜ `docker network create --subnet=<subnet> <nombre_de_red>`

Donde:

-   **subnet**: es la dirección IP de la subred de la red que estamos creando. Es importante tener en cuenta que esta dirección IP debe ser única y no debe entrar en conflicto con otras redes o subredes existentes en nuestro sistema.
-   **nombre_de_red**: es el nombre que le damos a la red que estamos creando.

Además de los campos mencionados anteriormente, también podemos utilizar la opción ‘**–driver**‘ en el comando ‘docker network create’ para especificar el controlador de red que deseamos utilizar.

Por ejemplo, si queremos crear una red de tipo “**bridge**“, podemos utilizar el siguiente comando:

➜ `docker network create --subnet=<subnet> --driver=bridge <nombre_de_red>`

En este caso, estamos utilizando la opción ‘**–driver=bridge**‘ para indicar que deseamos crear una red de tipo “**bridge**“. La opción –driver nos permite especificar el controlador de red que deseamos utilizar, que puede ser “**bridge**“, “**overlay**“, “**macvlan**“, “**ipvlan**” u otro controlador compatible con Docker.


## Escenarios

![00x500](Pasted%20image%2020230426163000.png)
Para el primer caso, solo podemos ver solo algunos puertos OPEN, como el puerto 80. Pero podemos hacer que la maquina victima haga peticiones **internas** (LocalHost) a sus puertos y servicios, para poder descubrir otros puertos abiertos como el 8089. 

En un SSRF podemos encontrar **utility.php** que nos ayudara a colocar dominios a traves del parametro **url**, con el cual podemos hacer lo siguiente:
	❯ Podemos colocar algún dominio para que nos muestre información acerca de algún servicio interno de la maquina, como su propio **localhost** 
	❯ Podemos encontrar información relevante como el User/Passwd del administrador

```bash 
❯ "http://172.17.0.2/utility.php?url=http://127.0.0.1:8089/login.html"
```
Podemos apuntar desde la maquina victima a su propio 'LocalHost' para poder ver servicios internos de la maquina.



![00x400](Pasted%20image%2020230427152444.png)

Para el segundo caso, podemos llegar a la maquina victima porque estamos en la misma subred '172.17.0.0', solo podemos ver el puerto 80 abierto. Pero en este caso existe un servicio que esta en otra subred,  la cual esta conectada con la primer maquina victima en la subred (10.10.0.0). Por lo que si quisiéramos acceder a ese servicio interno tendríamos que hacerlo por medio de la primer maquina victima.

```bash 
❯ curl "http://172.17.0.2/utility.php?url=http://10.10.0.3:8089/"
```
Estamos apuntando a la IP de la maquina victima y podemos cargar contenido que no esta expuesto al exterior de una web alojada en una maquina de una red interna de la empresa.


