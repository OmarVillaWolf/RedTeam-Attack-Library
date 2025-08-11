# PowerView 

Tags: #AD #Powershell #Enumeracion #Windows 

```bash 
# Descargamos la utilidad en nuestra maquina de atacante para despues pasar el archivo a la maquina victima que esta utilizando PowerShell

❯ wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1
```

```powershell 
❯ powershell -ep bypass                      # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas 


❯ . .\PowerView.ps1                          # Importamos el modulo
❯ Get-Domain                                 # Miramos el dominio 'FOREST'
❯ Get-Domain -Domain <Forest.name>           # Miramos informacion acerca de 'Parent domain'

❯ Get-DomainSID                              # Muestra el SID del dominio 

❯ Get-DomainPolicy                           # Muestra las politicas del dominio (KerberosPolicy, SystemAccess)
	(Get-DomainPolicy)."SystemAccess" 
	(Get-DomainPolicy)."KerberosPolicy"

❯ Get-DomainController                       # Muestra informacion del controlador de dominio (Roles, Domain, IP, Forest)
	Get-DomainController -Domain <Forest.name>

❯ Get-DomainUser                                  # Muestra informacion de los usuarios 
❯ Get-DomainUser | select samaccount, objectsid   # Muestra todos los usuarios 
❯ Get-DomainUser -Identity <Username>             # Muestra la info de un usuario en particular 
	Get-DomainUser -Identity <Username> -Properties DisplayName,MemberOf,Objectsis,useraccountcontrol | fl
		# fl = Format List 

❯ Get-NetUser -sPN | select samaccountname, servicepriuncipalname  # Usuarios que podemos encontrar por kerberos
❯ Get-NetUser -PreauthNotRequired | select samaccountname, useraccountcontrol # Usuarios que podemos encontrar por ASRP 

❯ Get-NetComputer                            # Enumeramos las computadoras existentes 
	Get-NetComputer | select name           # Desplegara todos los nombres de las computadoras 
	Get-NetComputer | select name, cn, operatingsystem 
❯ Get-NetComputer -Domain <Domain> | select cn, operatingsystem

❯ Get-NetGroup                               # Enumeramos los grupos existentes 
	Get-NetGroup | select name              # Muestra todos los nombres d elos grupos 
❯ Get-NetGroup 'Domain Admins' 
❯ Get-NetGroup -userName "username" | select name 

❯ Get-NetGroupMember "Domain Admins" | select MemberName   # Miras los miembros del grupo 'Admins'

❯ Get-NetShare                               # Miramos las carpetas compartidas 

❯ Get-NetGPO | select displayname            # Muestra la politca del dominio  

❯ Get-NetOU                                  # Muestra todas las unidades organizacionales (OU)
	Get-NetOU | select name, distinguishedname

❯ Get-NetDomainTrust                         # Miramos el Trust bidireccional, target name, trust type, etc... 

❯ Get-NetForest                              # Nombre, sitios, dominios 
❯ Get-NetForestTrust 
	Get-NetForestTrust -Forest <TargetName>
	Get-NetForest -Forest <TargetName>
❯ Get-NetForestDomain                         # Mirar el dominio actual del 'Forest'
	Get-NetForestDomain -Forest <TargetName>

❯ Get-DomainTrustMapping 

❯ Get-ObjectAcl -SamAccountName "users" -ResolveGUIDs


```

```bash 
❯ Find-DomainShare -ComputerName <Domain> -verbose         # Enumera las carpetas compartidas del sistema 
	Find-DomainShare -ComputerName <Domain> -CheckShareAccess -verbose
```

```bash 
❯ Find-InterestingDomainAcl -ResolveGUIs                   # Lista las ACL del dominio 
	Find-InterestingDomainAcl -ResolveGUIs | select IdentityReferenceName, ObjectDN, ActiveDirectoryRights 
```