# Enum Remote - NTDS.dit

Tags: #AD #ADModule #PowerView #DC #NTDS 

## ADModule 

El módulo de AD no genera alguna alerta ya que hace peticiones legítimas a diferencia de PowerView 

```bash 
La dll encargada de ejecutar todas las funciones se encuentra en la siguiente ruta del DC:
	- Ir a 'C:\Windows\Microsoft.NET\assembly\GAC_64\Microsoft.ActiveDirectory.Management'
	- La dll se llama 'Microsoft.ActiveDirectory.Management.dll'
```

```powershell 
# Extraer la DDL e mportarla en la máquina donde se usará el ADModule 
❯ Import-Module .\Microsoft.ActiveDirectory.Management.dll   
```

```powershell
! Usuario de dominio (AD)

# Enumeración de información del dominio de AD, como nombre del dominio, SID, Domain Controllers y modo funcional
❯ Get-ADDomain 

❯ Get-ADDomainController    # Obtener info del DC (Nombre, IP, etc...) 

# Enumerar todos los usuarios del dominio y muestra su nombre, tipo de objeto y GUID único de AD
❯ Get-ADUser -Filter * | select Name,ObjectClass,ObjectGuid
❯ Get-ADUser -Filter *      # Enumeración general 

# Enumerar todas las computadoras del dominio y muestra su DNSHostName, SamAccountName y Name
❯ Get-ADComputer -Filter * | select DNSHostName,SamAccountName,Name 
❯ Get-ADComputer -Filter *  # Enumeración general de equipos en el dominio 
```

## PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

El módulo de PowerView puede llegar a generar alertas inofensivas en los sistemas de seguridad 

```powershell 
❯ Import-Module .\PowerView.ps1        # Importar el módulo 
```

```powershell
! Usuario de dominio (AD)

❯ Get-NetDomain       # Enumerar algo de info general del DC (Forest, Domain)

❯ Get-NetDomainController   # Obtener info del DC (Nombre, IP, OS, etc...) 

# Enumerar usuarios del dominio y mostrar la última vez que iniciaron sesión o cerraron sesión
❯ Get-NetUser | select name,lastlogoff,lastlogon 
❯ Get-NetUser  # Enumeración de usuarios del DC con su info (SAMAccountNAme, ObjectGuid, logoncount, badpasswordtime, etc...)

# Enumerar todas las computadoras registradas en Active Directory
❯ Get-NetComputer     
❯ Get-NetComputer | select name,operatingsystemversion
# Enumerar computadoras del dominio y hace ping para identificar cuáles están activas, mostrando también su sistema operativo.
❯ Get-NetComputer -Ping | select name,operatingsystem
```

```powershell 
! Usuario de dominio (AD)

❯ Get-DomainPolicy    # Mirar las políticas generales así como las políticas de contraseñas y bloqueo de cuentas
❯ (Get-DomainPolicy)."SystemAccess"   # Filtrado solo a systemaccess
```

| Parámetro                    | Valor   | Significado                                        | Relevancia en pentesting                                    |
| ---------------------------- | ------- | -------------------------------------------------- | ----------------------------------------------------------- |
| MinimumPasswordAge           | 1 día   | Tiempo mínimo antes de poder cambiar la contraseña | Evita cambiar contraseñas repetidamente para evadir history |
| MaximumPasswordAge           | 42 días | Tiempo máximo antes de que expire la contraseña    | Las contraseñas se rotan cada 42 días                       |
| MinimumPasswordLength        | 4       | Longitud mínima de contraseña                      | Muy débil, fácil de crackear                                |
| PasswordComplexity           | 0       | Complejidad deshabilitada                          | No requiere mayúsculas, números o símbolos                  |
| PasswordHistorySize          | 24      | Número de contraseñas anteriores recordadas        | No se pueden reutilizar las últimas 24                      |
| LockoutBadCount              | 0       | Número de intentos fallidos antes de bloqueo       | No hay lockout de cuentas                                   |
| RequireLogonToChangePassword | 0       | No requiere logon para cambiar contraseña          | Configuración normal                                        |
| ForceLogoffWhenHourExpire    | 0       | No fuerza logout al expirar horas                  | No relevante para ataque                                    |
| ClearTextPassword            | 0       | No guarda contraseñas en texto plano               | Configuración segura                                        |
| LSAAnonymousNameLookup       | 0       | No permite lookup anónimo de nombres               | Limita enumeración anónima                                  |

Que se puede hacer:
1. Con "LockoutBadCount = 0" no existe bloqueo de cuentas por múltiple intentos.
2. Con "MinimumPasswordLength = 4" y "PasswordComplexity = 0" por tener contraseñas cortas sin complejidad se puede hacer password spraying, brute force y cracking offline.
3. Ataques recomendados: Password Spraying, Kerberoasting, ASRep Roasting, NTLLM Relay, crackeo de hashes.


```powershell 
! Usuario de dominio (AD)

❯ Get-DomainGroup     # Enumerar todos los grupos del dominio en AD
❯ Get-DomainGroup | select grouptype,name,description 

# Enumerar los miembros del grupo 'Administrators'
❯ Get-NetGroupMember -Identity "Administrators" | select MemberName

# Buscar recursos compartidos (SMB shares) accesibles en los equipos del dominio
❯ Find-DomainShare

# Enumerar todas las Organizational Units (OU) en AD
❯ Get-NetOU

# Enumerar todas las Group Policy Objects (GPOs) del dominio
❯ Get-NetGPO 
❯ Get-NetGPO | select displayname    # Enumerar los nombres de las GPOs

# Obtener las GPOs aplicadas en el equipo WS02 
❯ Get-NetGPO -ComputerIdentity WS02
# Obtener el nombre de la GPOs aplicadas en el equipo 
❯ Get-NetGPO -ComputerIdentity WS02 | select displayname

# Obtener las ACL (permisos) del objeto del usuario 'user1' en Active Directory
❯ Get-ObjectAcl -SamAccountName <user1>

# Enumerar todos los dominios que existen dentro del forest de AD 
❯ Get-NetForestDomain    
```

```powershell
! Usuario de dominio (AD)

# Probar en qué computadoras del dominio el usuario actual tiene privilegios de administrador local
❯ Find-LocalAdminAccess -Verbose

# Enumerar qué usuarios o grupos tienen privilegios de administrador local en computadoras del dominio
❯ Invoke-EnumerateLocalAdmin -Verbose

# Buscar en qué computadoras del dominio hay sesiones activas de usuarios del grupo "RDPUsers"
❯ Invoke-UserHunter -GroupName "RDPUsers" 
```