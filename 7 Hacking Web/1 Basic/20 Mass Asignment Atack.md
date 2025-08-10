# Ataques de asignación masiva (Mass-Asignment Attack) / Parameter Binding

Tags: #OWASP #Explotacion #ParameterBinding #MassAsignmentAttack 

El ataque de asignación masiva (**Mass Assignment Attack**) se basa en la manipulación de parámetros de entrada de una solicitud HTTP para crear o modificar campos en un objeto de modelo de datos en la aplicación web. En lugar de agregar nuevos parámetros, los atacantes intentan explotar la funcionalidad de los parámetros existentes para modificar campos que no deberían ser accesibles para el usuario.

Por ejemplo, en una aplicación web de gestión de usuarios, un formulario de registro puede tener campos para el nombre de usuario, correo electrónico y contraseña. Sin embargo, si la aplicación utiliza una biblioteca o marco que permite la asignación masiva de parámetros, el atacante podría manipular la solicitud HTTP para agregar un parámetro adicional, como el nivel de privilegio del usuario. De esta manera, el atacante podría registrarse como un usuario con privilegios elevados, simplemente agregando un parámetro adicional a la solicitud HTTP.

A continuación, se os proporciona el enlace directo al proyecto **Juice Shop** en Docker Hub, el cual nos permitirá desplegar un laboratorio vulnerable donde poder practicar esta vulnerabilidad:

-   **Juice Shop**: [https://hub.docker.com/r/bkimminich/juice-shop](https://hub.docker.com/r/bkimminich/juice-shop)


## Mass Asignment Attack

1. Sirve para que a la hora de registrar un usuario, podamos meter mas parámetros de los que deberíamos. 
2. 
![](Pasted%20image%2020230515182502.png)
Podemos observar la data que estamos enviando al servidor al momento de registrarnos en la pagina web. 

![](Pasted%20image%2020230515182631.png)

En la respuesta del servidor podemos observar que nos arroja un 'Success'. Esto quiere decir que el registro se ha hecho de manera correcta. Si el servidor no esta sanitizado. Podemos inyectar campos nuevos en el 'registro' de un usuario nuevo y poder agregar campos. 

Registro de un usuario con un campo inyectado llamado 'role:admin'. 

![](Pasted%20image%2020230515182919.png)

Respuesta del servidor.

![](Pasted%20image%2020230515183441.png)

Podemos observar que la respuesta del servidor nos agrega el parámetro que antes habíamos colocado. Ya que el servidor crea a ese usuario con todas las propiedades que le hayan llegado. 

2. Otro ejemplo en donde podemos inyectar campos para poder modificar al usuario y hacerlo admin.

![](Pasted%20image%2020230515184217.png)

Si la propiedad que queremos modificar no viaja en la data, podernos concatenarla de la misma manera que esta viajando la demás data, solo seria cuestión de ir probando como se llama el campo a cambiar y así hacer que cambie en este caso de false a true en el usuario. Esto de la siguiente manera. 

Ejemplos del campo a cambiar: 
(is_admin, isAdmin, isadmin, IsAdmin, admin, Admin, Administrator, administrator, privileged, privilege)

![](Pasted%20image%2020230515184550.png)

Respuesta del servidor en donde le cambiamos la data y ahora somos admin.

![](Pasted%20image%2020230515184820.png)