# Enumeración de ACL

Tags: #AD #Powershell #ACL 

```bash  
Listas de control de acceso (ACL):

- Es una lista de acceso de control de entradas (ACE) - ACE corresponde a los permisos individuales o acceso auditados. Quien tiene permiso y que puede hacer con ese objeto?

- Existen dos tipos:
	1. DACL: Define los permisos que los administradores (usuario o grupo) tienen sobre un objeto
	2. SACL: Registra los mensajes de éxito y fracaso cuando se accede a un objeto 
```

[![ACL-Mindmap.png | 900](https://i.postimg.cc/hPDjWBvz/ACL-Mindmap.png)](https://postimg.cc/z3Q5K4n8)

## Enumeración de ACLs con PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```powershell 
❯ . C:\AD\PowerView.ps1               # Cargar PowerView en memoria 
❯ Import-Module .\PowerView.ps1       # Importar el módulo 
```

```powershell 
# Obtener los ACLs asociados con un objeto específico 
❯ Get-DomainObjectAcl -SamAccountName user1 -ResolveGUIDs
   
# Obtener los ACLs asociados con un grupo específico e identificar...
❯ Get-DomainObjectAcl -Identity "Domain Admins" -ResolveGUIDs -verbose 
	- ObjectDN   :  CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local
	- ActiveDirectoryRights: Generic All
	- SecurityIdentifier: s-1-5-18

# Obtener los ACLs asociados con un prefijo específico para ser usado como búsqueda 
❯ Get-DomainObjectAcl -SearchBase "LDAP://CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local" -ResolveGUIDs -Verbose     

# Enumerar ACL usando el módulo de AD sin resolver GUIDs
❯ (Get-Acl 'AD:\CN=Administrator,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local').Access     

❯ Find-InterestingDomainAcl -ResolveGUIDs      # Buscar ACEs interesantes 

# Obtener ACLs asociados con un path específico 
❯ Get-PathAcl -Path "\\dcorp-dc.dollarcorp.moneycorp.local\sysvol"     
```