# Metodología en un DC

Tags: #AD #Enumeracion #Puerto #DC 

## Active Directory - Metodología Externa 

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

## Active Directory - Metodología Interna 

```bash 
Metodología general 

Tips utilizando las credenciales 'user:passwd' válidas:
	1.  Uyilizar 'PowerView' o 'ADModule' para enumerar:
	    - SAM Local
	    - Base de datos 'NTDS.dit' 
	
	2.  Recopilación de datos del DC con 'Sharphound' y compartir el archivo .zip a 'BlodHound' para enumerar:
		- ACLs
		- Users
		- GPOs
		- Grupos 


Notas:
	1. Si se ha comprometido un usuario y nos encontramos en un entorno Windows, nos podemos autenticar con ese otro usuario utilizando  runas en 'Powershell'  (Mejor opción)
	❯ runas /user:domain\user2 /netonly powershell         # Abrir una terminal powershell con autenticación local como el usuario host 'user1' y a manera de servicios de red estar autenticados como 'user2' 
	
	2. También, se puede utilizar ese usuario con una 'PowerShell' para hacer una autenticacion de red de la siguiente manera: 
			- En Windows 11 ir a Menú, buscar 'Powershell' y dar click derecho y seleccionar 'Open File Location'
			- En el simbolo de 'Powershell' dar 'Shift + click derecho' y seleccionar 'Run as different user'
			- Colocar las credenciales válidas de otro usuario para obtener una consola 
```