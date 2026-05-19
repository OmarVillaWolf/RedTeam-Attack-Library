# User Hunting 

Tags: #AD #Powershell #PowerView 

[![User-Hunting.png | 750](https://i.postimg.cc/9QFThwmg/User-Hunting.png)](https://postimg.cc/F781bRJ0)

```bash 
ID de eventos:

- 4624 = Logon exitoso. Un usuario inicio sesión correctamente 
- 4634 = Logoff. Un usuario cerró sesión 
- 4672 = Asignación de privilegios especiales. Un usuario inicia sesión y se le asignan privilegios especiales tipicamente por ser miembro de grupos privilegiados (Administrators, Domain Admins, etc...) 
```

## PowerView 

```powershell 
❯ Find-LocalAdminAccess -Verbose    # Encontrar todas las máquinas en el dominio actual donde el usuario actual tiene acceso 'admin'

❯ Get-NetComputer  # Esta función consulta el DC del dominio actual para obtener una lista de computadoras
❯ Invoke-CheckLocalAdminAccess   # Usar 'multi-threaded' para cada máquina 


Notas:
	1. Esto tambien se puede hacer con la ayuda de herraminetas de administración remota como WMI y Powershell. Bastante útil en los casos en que los puertos (RPC y SMB) utilizados por 'Find-LocalAdminAccess' son bloqueados
	2. Mirar 'Find-WMILocalAdminAccess.ps1' y 'Find-PSRemotingLocalAdminAccess.ps1'
```

## Desde afuera 

```powershell 
❯ . Find-PSRemotingLocalAdminAccess.ps1       # Importar el módulo 
❯ Find-PSRemotingLocalAdminAccess -Verbose    # Listar servidores del DC donde se tiene acceso administrativo local remoto
```

```powershell 
# Forma 1 de conectarse al server (Límitado a cmd.exe)
❯ winrs -r:dcorp-adminsrv cmd
```

```powershell 
# Forma 2 de conectarse al server (Es la mejor forma de conectarse) ya que te da una Powershell 
❯ Enter-PSSession -ComputerName server01.domain01.local    # Ingresar al server donde tenemos acceso administrativo remoto
	❯ $env:username
	❯ $env:computername
```

## Desde adentro de un server 

```powershell 
❯ Find-DomainUserLocation -Verbose       # Encontrar computadoras donde un admin del dominio (or specified user/group) tiene una sesión y se tiene acceso administrativo local para posible movimiento lateral
❯ Find-DomainUserLocation -ComputerName server02 
❯ Find-DomainUserLocation -UserGroupIdentity "RDPUsers"

❯ Get-DomainGroupMember     # Consultar el DC del dominio actual o proporcionado para los miembros del grupo dado (Admins de dominio por defecto)
❯ Get-DomainComputer        # Obtener una lista de computadoras 
❯ Get-NetSession / Get-NetLoggedon     # Obtener uns lista de sesiones y usuarios conectados 


Notas:
	1. Para el server 2019 y versiones posteriores, se requiere privilegios de administrador local para listar sesiones 
```

```powershell 
❯ Find-DomainUserLocation -CheckAccess    # Encontrar computadoras donde una sesión del admin del dominio esta dsiponible y el usuario actual tiene acceso admin 

❯ Find-DomainUserLocation -Stealth        # Encontrar computadoras (File servers and Distributed File Servers) donde una sesión admin del dominio esta disponible  
```

```powershell 
❯ winrs -r:dcorp-mgmt cmd /c "set computername && set username"  
# Ejecutar un comando en el server donde se tiene acceso administrativo
```

## Invoke-SessionHunter 

* [Invoke-SessionHunter](https://github.com/Leo4j/Invoke-SessionHunter)

```powershell 
No se necesita acceso admin en máquinas remotas. Usar 'Remote Registry y queries HKEY_USERS hive' 

❯ Invoke-SessionHunter -NoPortScan -RawResults | select Hostname,UserSession,Access   # Listar sesiones en las máquinas remotas y mirar si tienes acceso

❯ Invoke-SessionHunter -FailSafe    # Listar sesiones en máquinas remotas 

❯ Invoke-SessionHunter -NoPortScan -Targets servers.txt   # Buscar sesiones activas de usuarios en los servidores listados en servers.txt
```

```powershell 
# Hacerlo más Opsec 
❯ type servers.txt     # Crear una lista con los servers

❯ Invoke-SessionHunter -NoPortScan -RawResults -Targets servers.txt | select Hostname,UserSession,Access   
# Listar sesiones en las máquinas remotas y mirar si se tiene acceso
```

```powershell 
❯ Enter-PSSession -ComputerName server01.domain01.local    # Ingresar al server donde tenemos acceso administrativo remoto
	❯ $env:username
	❯ $env:computername
```