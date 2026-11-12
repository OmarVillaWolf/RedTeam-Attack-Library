# Enumeración de Políticas de Grupo 

Tags: #AD #Powershell #GPO 

```powershell 
Las politicas de grupo proveen la habilidad para manejar configuraciones y cambios fácilmente y centrados en AD.
Una configuración de política dolo puede afectar a un usuario o computadora 
	- Para computadoras: Configuración de seguridad, scripts de inicio y apagado, aplicaciones asignadas, etc..
	- Para usuarios: Configuración de seguridad, scripts de logon y logoff, aplicaciones asignadas y más 

Las políticas de grupo estan contenidas en un 'Group Policy Object - GPO'
1. GPO: Es una colección virtual de configuraciones de política, permisos de seguridad y 'scope of management - SOM' que se puede aplicar a usuarios y computadoras 
2. GPO pueden ser enlazadas a los dominios, sitios y unidades organizacionales 'OUs'
3. GPOs demasiado permisivos pueden ser abusados por varios ataques como 'privesc, backdoors, persistence, etc...'

Las organizaciones usan unidades organizacionales 'OUs' para delegar la administración 
1. OU es el nivel mas bajo del contenedor del AD para el cual un GPO puede ser aplicado 
2. Los ataques a los niveles de OU son debidos a la mal configuración o GPOs demasiado permisivos son más comunes en entornos empresariales. 
```

## Enumeración de GPOS con PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```powershell 
❯ . .\PowerView.ps1                # Cargar PowerView en memoria 
❯ Import-Module .\PowerView.ps1    # Importar el módulo 
```

```powershell 
❯ Get-DomainGPO               # Obtener una lista de los GPO en el dominio actual 
❯ Get-DomainGPO | select displayname 
❯ Get-DomainGPO -ComputerIdentity dcorp-user1        

❯ Get-DomainGPOLocalGroup     # Obtener GPO(s) el cual usa grupos restringidos o 'groups.xml' para usuarios 

# Obtener usuarios los cuales estan en un grupo local de una máquina usando GPO
❯ Get-DomainGPOComputerLocalGroupMapping -ComputerIdentity dcorp-user1   

# Obtener máquinas donde el usuario dado es miembro de un grupo específico 
❯ Get-DomainGPOUserLocalGroupMapping -Identity student1 -Verbose       
```

## Enumeración de GPOs con ActiveDirectory.psd1

* [Módulo AD](https://github.com/samratashok/ADModule/blob/master/ActiveDirectory/ActiveDirectory.psd1)

```powershell 
❯ Import-Module .\ActiveDirectory.psd1    # Importar el módulo 
```

```powershell 
❯ Get-ADOrganizationalUnit -Filter * -Properties *     # Usar el módulo de Active Directory 
```

### Groups 

```powershell 
Paso 1:
❯ Get-ADPrincipalGroupMembership -Identity student     # Identificar los grupos por membresía directa del usuario 

Paso 2:  IMPORTANTE (Muestra más grupos)
# Función para resolver membresía de grupos de forma recursiva — útil cuando un usuario pertenece a un grupo que a su vez pertenece a otro grupo (nested groups), y quieres ver toda la cadena de herencia de permiso

function Get-ADPrincipalGroupMembershipRecursive ($SamAccountName)
{
    $groups = @(Get-ADPrincipalGroupMembership -Identity $SamAccountName -ResourceContextServer <domain.local> | select -ExpandProperty distinguishedname)
    $groups
    if ($groups.count -gt 0)
    {
        foreach ($group in $groups)
        {
            Get-ADPrincipalGroupMembershipRecursive $group
        }
    }
}

# Ejecutar la función 
❯ Get-ADPrincipalGroupMembershipRecursive 'student'
❯ Get-ADPrincipalGroupMembershipRecursive -SamAccountName "student"

	CN=Domain Users,CN=Users,DC=us,DC=techcorp,DC=local
	CN=StudentUsers,CN=Users,DC=us,DC=techcorp,DC=local
	CN=Users,CN=Builtin,DC=us,DC=techcorp,DC=local
	CN=MaintenanceUsers,CN=Users,DC=us,DC=techcorp,DC=local
	CN=Managers,CN=Users,DC=us,DC=techcorp,DC=local
```

```powershell 
Paso 3:
# Identificar si hay una ACL en el grupo utilizando 'PowerView.ps1'
❯ Find-InterestingDomainAcl -ResolveGuiDs | ?{$_.IdentityReferenceName -match 'managers_group'}

	ObjectDN                  : CN=MachineAdmins,OU=Mgmt,DC=us,DC=techcorp,DC=local   <- IMPORTANTE 
	AceQualifier              : AccessAllowed
	ActiveDirectoryRights     : GenericAll    <- IMPORTANTE 
	ObjectAceType             : All
	AceFlags                  : ContainerInherit, Inherited
	AceType                   : AccessAllowedObject
	InheritanceFlags          : ContainerInherit
	SecurityIdentifier        : S-1-5-21-3753023061-306865128-3498081829-1115
	IdentityReferenceName     : Managers      <- IMPORTANTE 
	IdentityReferenceDomain   : us.techcorp.local
	IdentityReferenceDN       : CN=Managers,CN=Users,DC=us,DC=techcorp,DC=local
	IdentityReferenceClass    : group

Significa:
GenericAll lo tiene el grupo 'Managers' sobre el grupo 'MachineAdmins'. Por lo tanto, si tu usuario está en 'Managers' se puede agregar al grupo 'MachineAdmins'

```

```powershell
Paso 4:
# Mirar la descripción del grupo 
❯ Get-AdGroup -Identity MachineAdmins -Properties Description 
```

## Enumeración de OUs con PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```powershell 
❯ . .\PowerView.ps1                # Cargar PowerView en memoria 
❯ Import-Module .\PowerView.ps1    # Importar el módulo 
```

```powershell 
# Enumerar las unidades organizacionales  
❯ Get-DomainOU

❯ (Get-DomainOU -Identity Hybrid).distinguishedname | %{Get-DomainObject -Searchbase $_} | select name,samaccounttype

❯ (Get-DomainOU -Identity HybridConnect).distinguishedname | %{Get-DomainObject -Searchbase $_} | select name,samaccounttype
```

```powershell 
❯ Get-DomainOU     # Obtener OUs en un dominio 
❯ Get-DomainOU | select name 
❯ Get-DomainOU | select -ExpandProperty name    # Listar todos los OUs
❯ (Get-DomainOU -Identity 'OU').distinguishedname | %{Get-DomainComputer -SearchBase $_} | select name          # Listar todas las computadoras en un OU en específico 

❯ (Get-DomainOU -Identity DevOps).gplink    # Obtener el GPOname desde el atributo gplink 
❯ Get-DomainGPO -Identity '{0D1CC23D-1F20-4EEE-AF64-D99597AE2A6E}'     # Obtener GPO aplicado a un OU. Leer GPOname desde el atributo 'gplink' desde Get-NetOU 
	# Identity = Colocar el 'cn'
```

## Enumeración de OUs con ActiveDirectory.psd1

* [Módulo AD](https://github.com/samratashok/ADModule/blob/master/ActiveDirectory/ActiveDirectory.psd1)

```powershell 
❯ Import-Module .\ActiveDirectory.psd1    # Importar el módulo 
```

```powershell 
# Enumerar las unidades organizacionales  
❯ Get-ADObject -SearchBase 'OU=Hybrid,DC=us,DCtechcorp,DC=local' | select Name,ObjectClass 
❯ Get-ADObject -SearchBase 'OU=HybridConnect,DC=us,DCtechcorp,DC=local' | select Name,ObjectClass 

❯ Get-ADObject -Filter * -SearchBase 'OU=Hybrid,DC=us,DCtechcorp,DC=local' | select Name,ObjectClass 
```
