# Enumeración de dominio 

Tags: #Powershell #AD #PowerView #PowerHuntShares #PowershellModule 

## PowerHuntShares 

* [PowerHuntShares](https://github.com/NetSPI/PowerHuntShares)

```powershell 
PowerHuntShares sirve para descubrir recursos compartidos, archivos sensibles, ACLs para recursos compartidos, redes, computadoras, identidades, etc... y te genera un reporte HTML. 

❯ Import-Module C:\AD\Tools\PowerHuntShares.psm1        # Importar el módulo  
```

```powershell 
❯ Invoke-HuntSMBShares -NoPing -OutputDirectory C:\AD\Tools -HostList C:\AD\Tools\servers.txt 
```

## SharpView

* [SharpView](https://github.com/tevora-threat/SharpView)

## PowerView  

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```powershell 
❯ Import-Module .\PowerView.ps1       # Importar el módulo 
```

```powershell 
❯ Get-Domain                          # Obtener las características del dominio actual 
❯ Get-Domain -Domain domain1.corp     # Obtener las características de otro dominio 

❯ Get-DomainSID                       # Obtener el SID del dominio actual 

❯ Get-DomainPolicyData                # Politicas del dominio actual

❯ Get-DomainController                # Obtener los controladores del dominio actual 
❯ Get-DomainController -Domain domain1.corp    # Obtener los controladores de otro dominio 

❯ Get-DomainUser                      # Obtener lista de usuarios del dominio actual
❯ Get-DomainUser -Identity user1 
❯ Get-DomainUser | select samaccountname 
# Obtener todas las propiedades de los usuarios del dominio actual 
❯ Get-DomainUser -Identity user1 -Properties *    
❯ Get-DomainUser -Properties samaccountname, logonCount 
❯ Get-DomainUser -LDAPFilter "Description=*built*" | select name,Description   # Buscar una palabra en particular en los atributos del usuario 
❯ Find-DomainUserLocation    

❯ Get-DomainComputer | select Name     # Obtener una lista de computadores del dominio actual  
❯ Get-DomainComputer | select dnshostname, logonCount 
❯ Get-DomainComputer | select -ExpandProperty dnshostname 
❯ Get-DomainComputer -OperatingSystem "*Server 2022*"
❯ Get-DomainComputer -Ping 


❯ Get-DomainGroup | select Name        # Obtener todos los grupos del dominio actual 
❯ Get-DomainGroup -Domain <targetdomain>
❯ Get-DomainGroup *admin*              # Obtener todos los grupos conteniendo la palabra 'admin' 
❯ Get-DomainGroup *admin* | select Name 
❯ Get-DomainGroup *admin* -Domain domain1.corp | select Name  

❯ Get-DomainGroupMember -Identity "Domain Admins" -Recurse   # Obtener todos los miembros del grupo "Domain Admins"
❯ Get-DomainGroup -UserName "user1"    # Obtener el 'group membership' de un usuario 

❯ Get-NetLocalGroup -ComputerName dcorp-dc   # Lista todos los grupos locales en una máquina (Necesita privilegios de admin en 'non-dc machines')
❯ Get-NetLocalGroupMember -ComputerName dcorp-dc -GroupName Administrators   # Obtener los miembros del grupo local 'Administrators' en una máquina (necesita privilegios de admin en 'non-dc machines')

❯ Get-NetLoggedon -ComputerName dcorp-adminsrv          # Mirar logeo activos de usuarios en una computadora (necesita privilegios locales de admin en el target)
❯ Get-NetLoggedonLocal -ComputerName dcorp-adminsrv     # Obtener el logeo de usuarios localmente en una computadora (se necesita registro remoto en el target - started by-default on server OS)
❯ Get-NetLoggedon -ComputerName dcorp-adminsrv          # Obtener el ultimo logeo del usuario en una computadora (se necesita derechos administrativos y registro remnoto en el target)

❯ Invoke-ShareFinder -Verbose       # Encontrar recursos compartidos en un host del dominio actual 
❯ Invoke-FileFinder -Verbose        # Encontrar archivos sensibles en una computadora en el dominio 
❯ Get-NetFileServer                 # Obtener todos los servidores de archivos del dominio 
``` 

## Powershell Module 

* [PowerShell AD Module](https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2025-ps)
* [ADModule](https://github.com/samratashok/ADModule)

```powershell 
❯ Import-Module C:\AD\Tools\Microsoft.ActiveDirectory.Management.dll    # Importar el módulo
❯ Import-Module C:\AD\Tools\ActiveDirectory.psd1    # Importar el módulo
```

```powershell 
❯ Get-ADDomain                             # Obtener las características del dominio actual 
❯ Get-ADDomain -Identity domain1.corp      # Obtener las características de otro dominio 

❯ (Get-ADDomain).DomainSID                 # Obtener el SID del dominio actual 

❯ (Get-DomainPolicyData).systemaccess      # Politicas del dominio actual 
❯ (Get-DomainPolicyData -domain domain1.corp).systemaccess     # Mirar las politicas de otro dominio 

❯ Get-ADDomainController                   # Obtener los controladores del dominio actual
❯ Get-ADDomainController -DomainName domain1.corp -Discover    # Obtener los controladores de otro dominio 

❯ Get-ADUser -Filter * -Properties *       # Obtener lista de usuarios del dominio actual 
❯ Get-ADUser -Identity user1 -Properties *
# Obtener todas las propiedades de los usuarios del dominio actual 
❯ Get-ADUser -Filter * -Properties * | select -First 1 | Get-Member -MemberType *Property | select Name  
❯ Get-ADUser -Filter * -Properties * | select name,logonaccount,@{expression={[datatime]::fromFileTime($_.pwdlastset)}}
# Buscar una palabra en particular en los atributos del usuario 
❯ Get-ADUser -Filter 'Description -like "*built*"' -Properties Description | select name,Description 

❯ Get-ADComputer -Filter * | select Name    # Obtener una lista de computadores del dominio actual
❯ Get-ADComputer -Filter * -Properties * 
❯ Get-ADComputer -Filter 'OpertingSystem -like "*Server 2022*"' -Properties OperatingSystem | select Name,OperatyngSystem
❯ Get-ADComputer -Filter * -Properties DNSHostName | %{Test-Connection -Count 1 -ComputerName $_.DNSHostName}

❯ Get-ADGroup -Filter * | select Name     # Obtener todos los grupos del dominio actual
❯ Get-ADGroup -Filter * -Properties *
❯ Get-ADGroup -Filter 'Name -like "*admin*"' | select Name   # Obtener todos los grupos conteniendo la palabra 'admin'

❯ Get-ADGroupMember -Identity "Domain Admins" -Recursive    # Obtener todos los miembros del grupo "Domain Admins"
❯ Get-ADPrincipalGroupMembership -Identity user1            # Obtener el 'group membership' de un usuario
```