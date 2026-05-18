# Lab

Tags: #CRTP 


## Enumeración Local 
### CMD

```powershell 
❯ whoami          # Usuario actual con el que estás ejecutando la sesión
❯ whoami /all     # Tokens, privilegios, grupos y SID del usuario actual
❯ whoami /priv    # Privilegios asignados al token actual

❯ hostname        # Nombre del equipo actual
❯ systeminfo      # Información del sistema operativo, dominio, parches y arquitectura
```

```powershell 
❯ echo %COMPUTERNAME%    # Nombre del equipo desde variables de entorno
❯ echo %USERDOMAIN%      # Dominio al que pertenece el usuario actual

❯ set                    # Variables de entorno útiles (paths, usuarios, dominios, software)
❯ set username
```

```powershell 
❯ ipconfig /all    # Interfaces, DNS y relación con el dominio
❯ route print      # Rutas de red y posibles segmentos internos
```

```powershell 
❯ net user                         # Usuarios locales de la máquina
❯ net localgroup administrators    # Administradores locales del sistema
❯ net localgroup "Remote Desktop Users"    # Usuarios con acceso RDP
```

```powershell 
❯ query user    # Usuarios actualmente conectados
❯ qwinsta       # Sesiones activas y tipos de sesión
```

```powershell 
❯ tasklist /v    # Procesos ejecutándose y usuarios asociados
❯ sc query       # Servicios del sistema
❯ wmic service get name,pathname,startname    # Servicios y cuentas que los ejecutan
```

```powershell 
❯ net share    # Shares locales expuestos por la máquina
❯ net use      # Recursos SMB montados actualmente
```

```powershell 
❯ wmic product get name,version    # Software instalado
❯ sc query windefend    # Estado del servicio Windows Defender
```

### Powershell 

```powershell 
❯ Get-LocalUser           # Usuarios locales del sistema
❯ Get-LocalGroupMember Administrators    # Miembros del grupo local Administrators
❯ Get-NetTCPConnection    # Conexiones de red activas
```

```powershell 
❯ Get-MpComputerStatus    # Estado de Microsoft Defender
```

## Enumeración de AD 
### Powershell 

```powershell 
❯ Get-Domain    # Obtener las características del dominio actual 
```

```powershell 
❯ Get-DomainUser | select -ExpandProperty samaccountname    # Enumeración de usuarios (Nombre del usuario con el que inicia sesión)

❯ Get-DomainComputer | select -ExpandProperty dnshostname   # Enumeración de equipos del dominio (Nombre DNS/FQDN de cada máquina en AD)

❯ Get-DomainGroup -Identity "Domain Admins"              # Enumeración del grupo Domain Admins que esta en la DB ntds.dit en los DCs (Información y atributos del grupo de administradores del dominio)

❯ Get-DomainGroupMember -Identity "Domain Admins"        # Enumeración de miembros del grupo Domain Admins (Usuarios/grupos con privilegios administrativos sobre el dominio)

❯ Get-DomainGroupMember -Identity "Enterprise Admins"    # Enumeración de miembros del grupo Enterprise Admins (Usuarios/grupos con control administrativo sobre todo el forest) en el dominio actual 
❯ Get-DomainGroupMember -Identity "Enterprise Admins" -Domain domain01.local    # Enumeración de miembros del grupo Enterprise Admins en otro dominio/forest. 
❯ Get-DomainGroupMember -Identity "Enterprise Admins" -Domain domain01.local -Recurse | Select-Object MemberName, MemberSID

```

### ACL

```powershell 
# Buscar la ACL de un usuario en específico 
❯ Get-DomainUser | Where-Object {$_.samaccountname -like "Control"} | Select-Object samaccountname, objectsid   # Obtener el SID del grupo 

❯ Get-DomainObjectAcl -Identity "user" -Domain dollarcorp.moneycorp.local -ResolveGUIDs | Where-Object {$_.SecurityIdentifier -eq "S-1-5-21-719815819-3726368948-3917688648-1123"} | Select-Object ObjectDN, ActiveDirectoryRights, ObjectAceType  # Agregar el SID obtenido y el nombre del user
```

### GPO

```powershell 
❯ Get-DomainOU -Identity "StudentMachines" | Select-Object gplink, distinguishedname
❯ Get-DomainGPO -Identity "{7478F170-6A0C-490C-B355-9E4618BC785D}" | Select-Object displayname
```

## Trust 

```powershell 
❯ 
```


### ADModule 

```powershell 
❯ Import-Module .\Microsoft.ActiveDirectory.Management.dll
❯ Import-Module .\ActiveDirectory.psd1
```

```powershell 
❯ Get-ADUser -Filter *        # Enumerar todos los usuarios de dominio actual 
❯ Get-ADUser -Filter * -Properties *| select Samaccountname,Description    # Listar propiedades especificas, samaccount y descripciones para los usuarios 
❯ Get-ADComputer -Filter *    # Listar todas las computadoras  
❯ Get-ADGroupMember -Identity 'Domain Admins'   # Enumerar 'Domain Administrators' 
❯ Get-ADGroupMember -Identity 'Enterprise Admins' -Server domain01.local    # Enumerar 'Enterprise Administrators'
```