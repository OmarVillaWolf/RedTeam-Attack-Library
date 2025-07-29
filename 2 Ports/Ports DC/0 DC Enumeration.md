# Enumeración del DC

Tags: #AD #Enumeracion #Puerto #DC 

```powershell
❯ Puerto 53 DNS:
	Enumeración: 'Dig'

❯ Puerto 88 Kerberos: 
	Enumeración: 'Kerbrute' 
	
❯ Puerto 135 RPC: 
	Enumeración: 'RPCClient'

❯ Puerto 389 LDAP:
	Enumeración: 'Ldapsearch' 'Ldapdomaindump' 'GetADUsers'

❯ Puerto 445 SMB:
	Enumeración: 'Nextec' 'SMBClient' 'SMBMap' 

❯ Puerto 1433 MsSQLServer:
	Conexión: 'Impacket-mssqlclient'

❯ Puerto 5985, 5986 WinRM:
	Conexión: 'Evil-winrm'
```

## Active Directory 

```bash 
Metodología general 

Tips: 
	1. Descubir puertos en un DC '88 Kerberos, 389 LDAP, 636 LDAPS, 3268 GC'  
	2. Buscar el nombre del dominio y agregarlo al archivo '/etc/hosts' 'DC01.domain1.local domain1.local' obteniendolo con la herramienta 'Nextec'
	3. Enumeración (Shares, Usuarios) 
		1. Obtener usuario reales de un dominio 'https://app.snov.io/login'
		2. Obtener usuarios enumerando con 'SMB, RPC, WEB, LDAP'
	4. Validar usuarios en AD con 'Kerbrute' 
		1. Hacer ataque de diccionario con 'Kerbrute' para obtenerlos
		2. Estructura de un diccionario de usuarios en un DC: 'samsmith, sam.smith, ssmith, s.smith'
		3. Hacer ataque de diccionario con 'SMB' para obtenerlos, colocando la lista de usuarios como 'users y passwds'
	5. Si solo hay usuarios validos se puedo solicitar un TGT con un 'ASREProast attack' donde no se necesita disponer de credenciales validas y muestra el Hash de la password si el usuario tiene 'UF_DONT_REQUIRE_PREAUTH' 
	6. Si ya hay credenciales validas se puede obtener un TGS con 'Kerberoasting attack'
```
