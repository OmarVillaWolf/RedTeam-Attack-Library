# Session Puzzling / Session Fixation / Session Variable Overloading

Tags: #OWASP  #Enumeracion #SessionPuzzling #SessionFixatton #SessionVariableOverloading

Session Puzzling, Session Fixation y Session Variable Overloading son diferentes nombres correspondientes a vulnerabilidades de seguridad que afectan la **gestión de sesiones** en una aplicación web.

La vulnerabilidad de **Session Fixation** se produce cuando un atacante establece un identificador de sesión válido para un usuario y luego espera a que el usuario inicie sesión. Si el usuario inicia sesión con ese identificador de sesión, el atacante podría acceder a la sesión del usuario y realizar acciones maliciosas en su nombre. Para lograr esto, el atacante puede engañar al usuario para que haga clic en un enlace que incluye un identificador de sesión válido o explotar una debilidad en la aplicación web para establecer el identificador de sesión.

El término “**Session Puzzling**” se utiliza a veces para referirse a la misma vulnerabilidad, pero desde el punto de vista del atacante que intenta **adivinar** o **generar identificadores** de sesión válidos.

Por último, el término “**Session Variable Overloading**” se refiere a un tipo específico de ataque de Session Fixation en el que el atacante envía una gran cantidad de datos a la aplicación web con el objetivo de sobrecargar las variables de sesión. Si la aplicación web no valida adecuadamente la cantidad de datos que se pueden almacenar en las variables de sesión, el atacante podría sobrecargarlas con datos maliciosos y causar problemas en el rendimiento de la aplicación.

Para prevenir estas vulnerabilidades, es importante utilizar identificadores de sesión aleatorios y seguros, validar la autenticación y autorización del usuario antes de establecer una sesión y limitar la cantidad de datos que se pueden almacenar en las variables de sesión.


## Session Puzzling/Fixation/Overloading


Primero debemos de hacer login.
![](Pasted%20image%2020230522173634.png)

Después miramos que tenemos una Cookie inicial de Sesión que por los dos puntos en su estructura es un Json Web Token.
Header -> Antes del primer punto
Payload  -> Entre los dos puntos 
Signature -> Después del segundo punto 

* [Json-Web-Token](https://jwt.io/)

![](Pasted%20image%2020230522173619.png)


![](Pasted%20image%2020230522174842.png)

La pagina web anterior nos muestra los campos de la Cookie inicial cuando estamos autenticados.

Ahora en el lab nos salimos y nos vamos a 'Forgot Password'. Ahí colocamos el nombre del usuario e interceptamos la petición con BurpSuite. 

![](Pasted%20image%2020230522174111.png)

![](Pasted%20image%2020230522174137.png)


Una vez interceptada la petición, nos damos cuenta que nos coloca una 'Cookie de Sesión' en la respuesta.

![](Pasted%20image%2020230522174438.png)

Esta Cookie la podríamos usar para poder ver el Dahsboard del inicio ya que contempla los mismos campos que la Cookie anterior cuando estábamos autenticados.

![](Pasted%20image%2020230522174807.png)

Miramos que si en la url colocamos Dashboard podemos ver el contenido con esa nueva Cookie. 

![](Pasted%20image%2020230522175109.png)