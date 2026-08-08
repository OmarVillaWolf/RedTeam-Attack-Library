# ACLs

Tags: #AD #ACL #ACE #DACL

## Identificar ACEs

Para enumerarlas fácilmente es mejor hacerlo con la herramienta de BloodHound.

```powershell 
1 'ForceChangePassword': Permite cambiar la contraseña de un usuario objetivo sin conocer su valor actual. Si un atacante posee este ACE sobre un objeto 'user', puede forzar un password reset y tomar control de la cuenta directamente utilizando funciones como:
	❯ Set-DomainUserPassword

2 'AddMembers': Permite agregar usuarios, grupos o equipos arbitrarios a un grupo específico. Si el atacante tiene este ACE sobre un objeto `group`, puede añadirse a grupos privilegiados (por ejemplo, administradores) y escalar privilegios mediante:
	❯ Add-DomainGroupMember

3 'GenericAll': Proporciona control total del objeto, incluyendo la capacidad de añadir otros usuarios a un grupo, cambiar una contraseña de usuario sin conocer su valor actual, registrar un SPN con un objeto de usuario, etc.
	❯ Set-DomainUserPassword
	❯ Add-DomainGroupMember

4 'GenericWrite': Proporciona la capacidad de actualizar cualquier valor de parámetro de objeto de destino no protegido. Por ejemplo, actualizar el valor del parámetro "scriptPath" en un objeto de usuario de destino para hacer que ese usuario ejecute los comandos/ejecutables especificados la próxima vez que se conecte.
	❯ Set-DomainObject

5 'WriteOwner': Proporciona la capacidad de actualizar el propietario del objeto de destino. Una vez que el propietario del objeto ha sido cambiado a un usuario que el atacante controla, el atacante puede manipular el objeto de la manera que crea conveniente.
	❯ Set-DomainObjectOwner

6 'WriteDACL': Proporciona la capacidad de escribir una nueva ACE en la DACL del objeto objetivo. Por ejemplo, un atacante puede escribir una nueva ACE en la DACL del objeto de destino, dándole el "control total" del objeto de destino.
	❯ Add-NewADObjectAccessControlEntry

7 'AllExtendedRights': Proporciona la capacidad de realizar cualquier acción asociada con los derechos extendidos de Active Directory contra el objeto. Por ejemplo, añadir usuarios a un grupo y forzar el cambio de la contraseña de un usuario de destino.
	❯ Set-DomainUserPassword
	❯ Add-DomainGroupMember
```

## PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

El módulo de PowerView puede llegar a generar alertas inofensivas en los sistemas de seguridad 

```powershell 
❯ . .\PowerView.ps1                   # Cargar PowerView en memoria 
❯ Import-Module .\PowerView.ps1       # Importar el módulo 
```

```powershell 
# Listar todos los grupos para buscar el objetivo 'Administrators, Domain Admins, etc...'
❯ Get-DomainGroup | select grouptype,name,description    

❯ Get-DomainObjectAcl -Identity "Domain Admins"    # Mirar las diferentes ACE 'AceType' del objeto (ObjectSID), sus permisos 'ActiveDirectoryRights' y el SID

❯ Convert-SidToName S-1-5-21-4222...-2535..-512    # Convertir el SID a su nombre 

# Filtro
❯ Get-DomainObjectAcl -Identity "Domain Admins" | select SecurityIdentifier,AceType,ActiveDirectoryRights | f1

# Filtro mejorado para mirar que objetos tiene que privilegios en un grupo determinado
❯ Get-DomainObjectAcl -Identity "Domain Admins" | select @{name="Name";expression={Convert-SidToName $_.SecurityIdentifier}},AceType,ActiveDirectoryRights | f1
```

```powershell
# Escanear todas las ACE de todas las DACL de todos los objetos dentro del dominio con un valor mayor a 1000 (se ha creado despues del setup inicial) en el security identifier 
❯ Invoke-ACLScanner -ResolveGUIDs | select IdentityReferenceName,ObjectDN,ActiveDirectoryRights | f1  
```