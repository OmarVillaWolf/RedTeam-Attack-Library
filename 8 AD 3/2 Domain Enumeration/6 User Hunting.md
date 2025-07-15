# User Hunting 

Tags: #AD #Powershell #PowerView 

[![User-Hunting.png | 750](https://i.postimg.cc/9QFThwmg/User-Hunting.png)](https://postimg.cc/F781bRJ0)

```bash 
ID de eventos:

- 4624 = Logon exitoso. Un usuario inicio sesión correctamente 
- 4634 = Logoff. Un usuario cerró sesión 
- 4672 = Asignación de privilegios especiales. Un usuario inicia sesión y se le asignan privilegios especiales tipicamente por ser miembro de grupos privilegiados (Administrators, Domain Admins, etc...) 
```

```powershell 
❯ Find-LocalAdminAccess -Verbose    # Encontrar todas las maquinas en el dominio actual donde el usuario actual tiene acceso de admin

❯ Get-NetComputer  # Esta función consulta el DC del dominio actual para obtener una lista de computadoras
❯ Invoke-CheckLocalAdminAccess   # Usar 'multi-threaded' para cada maquina 


Notas:
	1. Esto tambien se puede hacer con la ayuda de herraminetas de administración remota como WMI y Powershell. Bastante útil en los casos en que los puertos (RPC y SMB) utilizados por 'Find-LocalAdminAccess' son bloqueados
	2. Mirar 'Find-WMILocalAdminAccess.ps1' y 'Find-PSRemotingLocalAdminAccess.ps1'
```

```powershell 
❯ . Find-PSRemotingLocalAdminAccess.ps1       # Importar el módulo 
❯ Find-PSRemotingLocalAdminAccess -Verbose    # Listar servidores del DC donde se tiene acceso administrativo local 
```

```powershell 
❯ Find-DomainUserLocation -Verbose       # Encontrar computadoras donde un admin del dominio (or specified user/group) tiene una sesión
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

## Invoke-SessionHunter 

* [Invoke-SessionHunter](https://github.com/Leo4j/Invoke-SessionHunter)

```powershell 
No se necesita acceso admin en maquinas remotas. Usa 'Remote Registry y queries HKEY_USERS hive' 

❯ Invoke-SessionHunter -FailSafe    # Lista sesiones en maquinas remotas 

❯ Invoke-SessionHunter -NoPortScan -Targets C:\AD\Tools\servers.txt   # Comando amigable y evita conectarse a todos los targets y solo especificar objetivos  
```