# Prototype Pollution

Tags: #PrototypePollution #OWASP #Explotacion

El ataque **Prototype Pollution** es una técnica de ataque que aprovecha las vulnerabilidades en la implementación de objetos en JavaScript. Esta técnica de ataque se utiliza para modificar la propiedad “**prototype**” de un objeto en una aplicación web, lo que puede permitir al atacante ejecutar código malicioso o manipular los datos de la aplicación.

En JavaScript, la propiedad “prototype” se utiliza para definir las propiedades y métodos de un objeto. Los atacantes pueden explotar esta característica de JavaScript para modificar las propiedades y métodos de un objeto y tomar el control de la aplicación.

El ataque de Prototype Pollution se produce cuando un atacante modifica la propiedad “prototype” de un objeto en una aplicación web. Esto se puede lograr mediante la manipulación de datos que se envían a través de formularios o solicitudes AJAX, o mediante la inserción de código malicioso en el código JavaScript de la aplicación.

Una vez que el objeto ha sido manipulado, el atacante puede ejecutar código malicioso en la aplicación, manipular los datos de la aplicación o tomar el control de la sesión de un usuario. Por ejemplo, un atacante podría modificar la propiedad “prototype” de un objeto de autenticación de usuario para permitir el acceso a una cuenta sin la necesidad de una contraseña.

El impacto de la explotación del ataque Prototype Pollution puede ser significativo, ya que los atacantes pueden tomar el control de la aplicación o comprometer los datos de los usuarios. Además, dado que el ataque se basa en vulnerabilidades en la implementación de objetos en JavaScript, puede ser difícil de detectar y corregir.

A continuación, se proporciona el enlace directo al proyecto de Github que utilizamos en esta clase para desplegar un entorno vulnerable con el que poder practicar:

-   **SKF-LABS**: [https://github.com/blabla1337/skf-labs](https://github.com/blabla1337/skf-labs)


## Prototype-Pollution 

* [Prototype-Pollution-Explain](https://medium.com/node-modules/what-is-prototype-pollution-and-why-is-it-such-a-big-deal-2dd8d89a93c)

Esto hace un **Merge** entre dos objetos y sus propiedades.
Si no encuentra la propiedad para el objeto actual, lo que hace es buscarla en el prototipo y si lo hemos modificado anteriormente, este lo hereda y asi podríamos hacer el 'PrototypePollution'

* Contaminaremos el prototipo para que la propiedad **IsAdmin = True** y con eso logremos ser usuario 'Admin'

![](Pasted%20image%2020230510162735.png)

Aquí podemos ver que la contaminación se hizo colocando la parte de **"\_\_proto\_\_:{"isAdmin":true}"**

* Si los objetos no tienen propiedades, pero logramos contaminar con el 'proto' podemos hacer que hereden la propiedad
![](Pasted%20image%2020230510163110.png)


Debemos de modificar la data interceptada y pasarla a formato Json. Este ejemplo a continuación es para el lab que nos descargamos.
![](Pasted%20image%2020230510163455.png)

![](Pasted%20image%2020230510163714.png)

Esto es lo que debemos de agregar y así podríamos contaminar el prototipo para poder ser admin.