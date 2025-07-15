# Enumeración de ACL

Tags: #AD #Powershell #ACL 

```powershell 
Listas de control de aceeso (ACL):

- Es una lista de control de entradas (ACE) - ACE corresponde a los permisos individuales o acceso auditados. Quien tiene permiso y que puede hacer con ese objeto?

- Existen dos tipos:
	1. DACL: Define los permisos que los administradores (usuario o grupo) tienen sobre un objeto
	2. SACL: Registar los mensajes de exito y fracaso cuando se accede a un objeto 
```

[![ACL-Mindmap.png | 900](https://i.postimg.cc/hPDjWBvz/ACL-Mindmap.png)](https://postimg.cc/z3Q5K4n8)

## PowerView 

```powershell 
❯ Get-DomainObjectAcl -SamAccountName user1 -ResolveGUIDs     # Obtener los ACLs asociados con un objeto especifico 
❯ Get-DomainObjectAcl -Identity "Domain Admins" -ResolveGUIDs -verbose 
❯ Get-DomainObjectAcl -SearchBase "LDAP://CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local" -ResolveGUIDs -Verbose     # Obtener los ACLs asociados con un prefijo especifico para ser usado como busqueda 

❯ (Get-Acl 'AD:\CN=Administrator,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local').Access     # Enumerar ACL usando el modulo de AD sin resolver GUIDs

❯ Find-InterestingDomainAcl -ResolveGUIDs       # Buscar ACEs interesantes 

❯ Get-PathAcl -Path "\\dcorp-dc.dollarcorp.moneycorp.local\sysvol"     # Obtener ACLs asociados con un path especifico 
```