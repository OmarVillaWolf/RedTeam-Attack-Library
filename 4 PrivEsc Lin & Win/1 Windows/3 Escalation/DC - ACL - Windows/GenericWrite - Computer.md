# Abuso ACL 

Tags: #AD #ACL #Windows 

## GenericWrite sobre Computador

* [Abusin-AD-ACLs-ACEs](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces)
* [HackTricks-Abusing-AD-ACLs-ACE](https://book.hacktricks.xyz/es/windows-hardening/active-directory-methodology/acl-persistence-abuse)

Si tenemos esta ACL sobre un objeto Computer, permite modificar determinados atributos del objeto Computer en Active Directory. En este escenario, se aprovecha para modificar la configuración necesaria del Computer y continuar con RBCD.

```powershell
# Paso 1: Obtener el objeto del usuario que posee GenericWrite
❯ $user = Get-ADUser -Identity "compwrite.user"

# Paso 2: Obtener su SID
❯ $SID = $user.SID

# Paso 3: Comprobar que compwrite.user tiene GenericWrite sobre el Computer objetivo
❯ Get-ObjectAcl -SamAccountName First-DC -ResolveGUIDs | ?{$_.ActiveDirectoryRights -eq "GenericWrite" -and $_.SecurityIdentifier -eq $SID} | Select AceType,ActiveDirectoryRights,ObjectDN | Format-List


# Otra forma de poner el SID pero antes se debe agregar a la variable convertido 
❯ Get-ObjectAcl -SamAccountName First-DC -ResolveGUIDs | ?{$_.ActiveDirectoryRights -eq "GenericWrite" -and $_.SecurityIdentifier -eq $SID } | select AceType,ActiveDirectoryRights,ObjectDN | fl

# Objetivo:
# Confirmar que compwrite.user tiene GenericWrite sobre First-DC.
# GenericWrite → modificar atributos del Computer → configurar RBCD → continuar la escalada.


NOTA
	- Despues de esto sigue el RBCD
```