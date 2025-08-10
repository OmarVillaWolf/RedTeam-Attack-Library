# Enumeración de servicio HTTP

Tags: #Web #Reconocimiento #Escaneo  #HTTP #HTTP3 #Metodos

## Banner Grabbing 

* Banner Grabbing: Es una técnica para reunir información usada por los pentesters para enumerar información del sistema operativo 'target', así, como los servicios que están corriendo en los puertos abiertos.  El objetivo primario es identificar el servicio que esta corriendo en un puerto especifico así como la versión del servicio. El banner grabbing se ejecuta a través de varias técnicas:
	* Ejecutar una detección de servicio con Nmap.
	* Conectarse a un puerto abierto con Netcat. 
	* Autenticarse con el servicio (Si el servicio soporta la autenticación) por ejemplo: SSH, FTP, Telnet, etc...

```bash 
1. Usar Nmap para ver los puertos abiertos, versiones, SO, banner, etc...
❯ nmap -sV --script=banner <IP>

1. Usar Netcat para conectarse a un puerto abierto, a veces nos muestra el 'Banner' 
❯ nc <IP> <Port>           

1. Usar Searchsploit para buscar algna vulnerabilidad en la version del puerto abierto
❯ searchsploit <servicio> <version>

1. Autenticarnos son el servicio, a veces nos muestra el 'Banner' 
❯ ssh <user>@<IP> 
```

## Practicar 

```bash 
1. svn checkout https://github.com/vulhub/vulhub/trunk/openssl/CVE-2014-0160
```

## Buscar en la Web

```bash 
# Que podemos buscar en una pagina Web
1. Script de Nmap para 'http'
2. 'Whatweb / Wappalyzer' Identificar tecnologías 
3. Mirar el código fuente 'Ctrl + u'
4. Inspeccionar el código 'Ctrl + Shift + c'
5. Robots.txt / Sitemap.xml
6. Métodos permitidos 'Curl'
7. 'Fuzzing o Burpsuite'
	1. Directorios (Si se encuentra un /admin con código '301' agregarlo a la ruta y buscar por extensiones como: '.php')
	2. Subdominios
	3. Archivos como: .php, .html, .txt, .asp, .aspx 
8. Nikto (Mirar vulnerabilidades)
9. Rastreo pasivo 'Burpsuite' crawling

# Subdominios 
1. Sublist3r
2. Dnsrecon
3. OSINT (crt.sh)
4. Google Dorks

# Enumeracion de usuarios 
1. Ffuf (Agregando cabeceras y contenido cuando encontramos 'username already exists')
2. Fuerza bruta (Cuando ya tenemos algunos nombres de usuarios)

# Atajos en la Web
Recargar la página 'Ctrl + r'
Abrir una página en otra pestaña 'Ctrl + Click-Izquierdo'

# Data sensible Expuesta
1. Weak Password Storage 
2. Information Disclosure in Error Messages (Banner)
3. Directory Traversal 
4. Unencrypted Backups
```

## Si existe un folder '.GIT' 

* [Git_Hacker](https://github.com/rbozburun/git-hacker)
* [Git_Dump](https://github.com/Jenderal92/git-dump)
* [GitHack](https://github.com/lijiejie/GitHack)

```bash 
# Tools para obtener el projecto completo 

❯ git_hacker.py 
❯ git-dump.py 

❯ python3 GitHack.py http://web.com/.git/     # Extraer todos los recursos del proyecto de GIT
```

## Usuarios por defecto 

```bash 
# Diferentes tipos de usuario en los SO

En Linux:    www-data, User, Root          
En Windows:  iss apppool\defaultapppool, User, NT Authority\System            
```

## Ruta típica en consola

```python 

/var/www/html/
	config.php                  # Archivo de configuracion de la aplicación web y puede contener informacion valiosa
	initialize.php              # Archivo que se crea con las instalación de la web (Similar a wp-config) ya que puede contener credenciales de acceso 'user:passwd' para la DB
```

## Comandos

* [Cyberchef](https://gchq.github.io/CyberChef/)

```bash 
❯ cadaver http://IP/dir/

	❯ ls                           # Listar el contenido del directorio 
	❯ put cmd.php                  # Subir un archivo al directorio 
```

```bash
❯ http ❮IP❯                         # Ver las cabeceras 
```

```bash 
❯ browsh --startup-url ❮IP❯         # Enumeración del buscador de un Apache en forma de GUI
```

```bash 
❯ lynx http://❮IP❯                  # Enumeración del buscador en fomra de GUI
```

```bash
❯ whatweb ❮http://❮IP❯              # Obtener una breve descripción del gestor de contenidos, así como el servidor y algunas tecnologías utilizadas 

	# Mirar la jQuery
	# Servidor Web
	# Tecnologías 
```

```bash
❯ whatweb ❮http://❮IP❯:❮PORT❯       # Obtener una breve descripción del gestor de contenidos por un puerto especifico
```

```bash
❯ whatweb ❮http://❮IP❯ -v

	# v = Mirar las cabeceras de la pagina web
```

```bash 
❯ wig http://❮IP❯        # WebApp Information Gathered recopila info de la aplicación web y reporta las versiones de los servicios en la web, el CMS 'Content Management System' que se esta utilizando, vulnerabilidades, etc...
```

```bash 
❯ lbd http://❮IP❯        # Identificar el servicio de HTTP-Load Balancing del servidor   
❯ lbd domain.com         # Si se coloca un dominio de internet 
```

```bash
❯ curl ❮IP❯ | bash       # Obtener un index.html del servidor y con 'bash' se interpretará la data 

❯ curl ❮IP❯ | more       

❯ curl http://IP/cgi-bin/ | more    # Mirar las cabeceras de ese directorio
```

```bash 
❯ wget "http://❮IP❯/index"           # Descargar el archivo index 
```

## Banner Grabbing 

```bash
❯ curl -s -X GET http://❮IP❯ -I      # Hacer 'Banner grabbing' y obtener info del 'ETag, Server, etc...'
❯ curl http://❮IP❯ -v                # Mirar los headers de la pagina web 'Request '

❯ telnet IP 80                       
	GET / HTTP/1.0                  # Dar dos enter para obtener la información
	
❯ nc IP 80 
	HEAD / HTTP/1.0                 # Dar dos enter para obtener la información
	OPTIONS http://IP HTTP/1.0      # Mirar la página web en código HTML y sus opciones permitidas 'GET,POST,etc...'
	host:IP
```

## HTTP3

Es la próxima y tercera versión principal del Protocolo de Transferencia de Hipertexto utilizado para intercambiar información en la World Wide Web, que sucederá a HTTP/2. HTTP/3 es un borrador basado en un RFC anterior, entonces nombrado "Protocolo de Transferencia de Hipertexto sobre QUIC". 
	Protocolo **UDP**
    [HTTP3-QUIC](https://github.com/cloudflare/quiche)
Para poder usar la herramienta de arriba debemos de instalar lo siguiente:
	[Rush](https://github.com/rust-lang/rustup/issues/686)

```bash
# Ruta para poder ejecutar el http3-client /home/user/quiche/target/debug/examples

❯ ./http3-client ❮https://127.0.0.1❯
```