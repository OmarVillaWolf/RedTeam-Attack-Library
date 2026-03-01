# Enumeración del DC

Tags: #AD #Enumeracion #Puerto #DC 

```powershell
❯ Puerto 53 DNS:
	Enumeración: 'Dig' → Consultar servidores DNS y obtener info sobre nombres de dominio 

❯ Puerto 88 Kerberos: 
	Enumeración: 'Kerbrute' → Enumeración de usuarios 
	
❯ Puerto 135 RPC: 
	Enumeración: 'RPCClient' → Enumeración de usuarios, grupos 

❯ Puerto 389 LDAP:
	Enumeración: 'Ldapsearch' 'Ldapdomaindump' 'GetADUsers' → Enumeración de usuarios, grupos

❯ Puerto 445 SMB:
	Enumeración: 'Nextec' 'SMBClient' 'SMBMap' → Enumeración de directorios compartidos 

❯ Puerto 1433 MsSQLServer:
	Conexión: 'Impacket-mssqlclient' → Enumeración de la base de datos 

❯ Puerto 5985, 5986 WinRM:
	Conexión: 'Evil-winrm' → Conexión a una máquina Windows 
```

## Active Directory External 

```bash 
Metodología general 

Tips: 
	1. Descubir puertos en un DC '88 Kerberos, 389 LDAP, 636 LDAPS, 3268 GC'  
	
	2. Buscar el nombre del dominio y agregarlo al archivo '/etc/hosts' 'DC01.domain1.local domain1.local' obteniendolo con la herramienta 'Nextec'
	
	3. Enumeración (Shares, Usuarios) 
		1. Obtener usuario reales de un dominio 'https://app.snov.io/login'
		2. Obtener usuarios enumerando con 'SMB, RPC, WEB, LDAP'
	
	4. Usuarios de AD con 'Kerbrute' 
		1. Ejecutar ataque de diccionario con 'Kerbrute' para obtener usuarios válidos 
		2. Ejecutar ataque de diccionario con 'SMB' para obtener usuario válidos creando una lista de usuarios: 'users y passwds'
		- Estructura de un diccionario de usuarios en un DC: 'samsmith, sam.smith, ssmith, s.smith'
	
	5. Si solo se tiene usuarios válidos, se puedo solicitar un TGT con un 'ASREProast attack' para obtener el 'hash' de la password si el usuario tiene configurado 'UF_DONT_REQUIRE_PREAUTH' 
	
	6. Si ya hay credenciales válidas 'user:passwd' se puede obtener un TGS de otro usuario con un 'Kerberoasting attack' 
```

## Active Directory Internal 

```bash 
Metodología general 

Tips: 
	1. Utilizar las credenciales 'user:passwd' válidas para hacer una recopilación de datos con 'Sharphound' y mirar las ACLs en 'BlodHound'
```