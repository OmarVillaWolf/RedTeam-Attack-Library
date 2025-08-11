# Abuso ACL 

Tags: #AD #ACL #Windows 

## GenericWrite sobre Computador

* [Abusin-AD-ACLs-ACEs](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces)
* [HackTricks-Abusing-AD-ACLs-ACE](https://book.hacktricks.xyz/es/windows-hardening/active-directory-methodology/acl-persistence-abuse)

```powershell
❯ $user = Get-ADUser -Identity "compwrite.user"    # Creamos una variable con el objeto 'usuario'
❯ $user.SID                                        # Ver el atributo SID del objeto 'usuario'


❯ Get-ObjectAcl -SamAccountName First-DC -ResolveGUIDs | ?{$_.ActiveDirectoryRights -eq "GenericWrite" -and $_.SecurityIdentifier -eq "S-1-5-21-1861162130-2580302541-221646211-1124" } | select AceType,ActiveDirectoryRights,ObjectDN | fl

# Otra forma de poner el SID pero antes se debe agregar a la variable convertido 
❯ Get-ObjectAcl -SamAccountName First-DC -ResolveGUIDs | ?{$_.ActiveDirectoryRights -eq "GenericWrite" -and $_.SecurityIdentifier -eq $SID } | select AceType,ActiveDirectoryRights,ObjectDN | fl

Nota: Despues de esto sigue el RBCD
```