# Enum Remote - NTDS.dit

Tags: #AD #ADModule #PowerView #DC #NTDS 

## ADModule 

```bash 
El módulo de AD no genera alguna alerta ya que hace peticiones legítimas a diferencia de PowerView 
```

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
# Enumeración de información general del DC (Forest, Domain, etc...)
❯ Get-ADDomain 

❯ Get-ADDomainController    # Obtener info del DC (Nombre, IP, etc...) 

❯ Get-ADUser -Filter *      # Obtener todos los usuarios del DC con algo de info (SAMAccountNAme, ObjectGuid, etc...)
❯ Get-ADUser -Filter * | select Name,ObjectClass,ObjectGuid   # Filtrado 

❯ Get-ADComputer -Filter *  # Obtener todos los equipos en el dominio 
❯ Get-ADComputer -Filter * | select DNSHostName,SamAccountName,Name 
```

## PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```bash 
El módulo de PowerView puede llegar a generar alertas inofensivas en los sistemas de seguridad 
```

```powershell 
❯ . .\PowerView.ps1                    # Cargar PowerView en memoria 
❯ Import-Module .\PowerView.ps1        # Importar el módulo 
```

```powershell
❯ Get-NetDomain       # Enumerar algo de info general del DC (Forest, Domain)

❯ Get-DomainPolicy    # Mirar las políticas 
❯ (Get-DomainPolicy)."SystemAccess"

❯ Get-NetDomainController   # Obtener info del DC (Nombre, IP, OS, etc...) 

❯ Get-NetUser  # Obtener todos los usuarios del DC con su info (SAMAccountNAme, ObjectGuid, logoncount, badpasswordtime, etc...)
❯ Get-NetUser | select name,lastlogoff,lastlogon 
 
❯ Get-NetComputer     # Obtener todos los equipos en el dominio (OS, Name, etc...)
❯ Get-NetComputer | select name,operatingsystemversion
❯ Get-NetComputer -Ping | select name,operatingsystem   # Saber si el equipo esta activo o no 
```

```powershell 
❯ Get-DomainGroup     # Obtener toda la info de todos los grupos del DC 
❯ Get-DomainGroup | select grouptype,name,description 

# Enumerar los usuarios que pertenecen al grupo 'Administrators'
❯ Get-NetGroupMember -Identity "Administrators" | select MemberName

❯ Find-DomainShare    # Obtener toda la info de los recursos compartidos del DC

❯ Get-NetOU           # Obtener toda la info de las unidades organizativas en el DC

❯ Get-NetGPO          # Obtener toda la info de las GPOs en el DC
❯ Get-NetGPO | select displayname    # Enumerar los nombres de las GPOs
# Obtener toda la info de las GPO que se estan aplicando en el equipo 
❯ Get-NetGPO -ComputerIdentity WS02
# Obtener el nombre de la GPO que se estan aplicando en el equipo 
❯ Get-NetGPO -ComputerIdentity WS02 | select displayname

❯ Get-ObjectAcl -SamAccountName user1     # Obtener las ACL de un objecto (usuario)

❯ Get-NetForestDomain    # Enumerar el forest 
```

```powershell
❯ Find-LocalAdminAccess -Verbose         # Buscar máquinas donde el usuario sea admin local 

❯ Invoke-EnumerateLocalAdmin -Verbose    # Enumerar usuarios admin locales en los dif equipos

❯ Invoke-UserHunter -GroupName "RDPUsers" # Buscar sesiones de un usuarios que pertenezca al grupo 'RDP Users'
```