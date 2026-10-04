# Metodología Windows y AD 

Tags: #AD #ActiveDirectory #Metodologia #Kali #Windows 

## SIN CREDENCIALES 

### EN DC
```bash 
## RECONOCIMIENTO

- Enumeración puerto 445 'SMB'
  	- Enumerar usuarios (--rid-brute, --users)
	- Investigar Shares
		- Investigar carpeta de SYSVOL, NETLOGON o Personalizadas 
		- Buscar archivos con credenciales  <-  IMPORTANTE

- Enumeración puerto 135 'RPC'
	- Enumerar con nullsession en busca de usuarios
	- RID CYCLING (Si no se puede ingresar por null session)

- Enumeración puerto 389/636 'LDAP'
	- Mapear toda la info con 'ldapdomaindump'
	- A veces muestra credenciales  

- Enumeración puerto 88 'Kerberos'
	- Kerbrute para validar buscar usuarios válidos (BruteForce)

- Enumeración puerto 80 'web'
	- Buscar archivos con credenciales  <-  IMPORTANTE


NOTA:
	- Si se encuentra una password con un año en especial, a veces es bueno colocarle el siguiente año o el actual para el password Spraying
```

```bash 
## ATAQUES 

- ASReproast Attack (Si se tiene solo el usuario sin passwd) 
- Relays 
	- SMB Writable Share -> Slinky -> Malicious LNK / NTLM Authentication Capture
	- Library-ms
```

## CON CREDENCIALES 

### EN SERVER
```bash 
Pasos:
- Verificar si el usuario dado puede ingresar por 'WinRM, RDP' 
	- Si se obtiene un usuario por 'dump (SAM)' agregar el parámetro --local-auth en 'Netexec' para esos usuarios 
		- Cracker hashes NT con John 
		- Verificar si el usuario dado puede ingresar por 'WinRM, RDP' con --local-auth

- Enumeración puerto 445 'SMB'
	- Enumerar usuarios (--users, --rid-brute)
	    - Ataque de bruteforce 'users.txt:users.txt' 
	    - Password Spraying (Misma password dada al inicio)
	- Investigar Shares (Revisar de cada usuario nuevo obtenido)
		- Investigar carpeta de SYSVOL (Common Vulnes), NETLOGON o Personalizadas 
		- Buscar archivos con credenciales  <-  IMPORTANTE 

NOTA:
	- Si se encuentra una password con un año en especial, a veces es bueno colocarle el siguiente año o el actual para el password Spraying 
```

```bash 
## ATAQUES 

- Relays 
	- SMB Writable Share -> Slinky -> Malicious LNK / NTLM Authentication Capture
	- Library-ms
```

### EN DC
```bash 
## RECONOCIMIENTO 

Pasos:
- Enumeración puerto 445 'SMB'
	- Enumerar usuarios (--users, --rid-brute)
	    - Ataque de bruteforce 'users.txt:users.txt' 
	    - Password Spraying (Misma password dada al inicio)
	- Investigar Shares (Revisar de cada usuario nuevo obtenido)
		- Investigar carpeta de SYSVOL (Common Vulnes), NETLOGON o Personalizadas 
		- Buscar archivos con credenciales  <-  IMPORTANTE 

- Verificar si el usuario dado puede ingresar por 'WinRM, RDP'

- Enumeración puerto 389/636 'LDAP'
	- Mapear toda la info con 'ldapdomaindump'
	- A veces muestra credenciales  

- Enumeración puerto 80 'web'
	- Buscar archivos con credenciales  <-  IMPORTANTE
	- Buscar si existe un login e ingresar con las credenciales 

- Enumeración con BloodHound 
	- Enabled: FALSE  = Cuenta deshabilitada, por lo tanto toca habilitarla con LDAP
	- Buscar Outbound Object Control (ACLs) 
	- Buscar usuarios Kerberosteables 
	- Shortest Path to Domain Admin 
	- Shortest Path from Owned objects
```

```bash 
## ATAQUES 

- BloodHound 
  	- LogonScript (Dir WRITE)
	- Abuso de ACLs 
		* En BloodHound hay que seleccionar el usuario o grupo que tiene la ACL porque luego no la muestra bien la consola
	- Kerberoasting Attack 
	- ADCS Attacks 

- Relays 
	- NTLM Coercion Attack - Service Account (Acceder a un panel web y proporcionar ruta //IP_Kali/test) para capturar el HASH con "responder" 
	- SMB Writable Share -> Slinky -> Malicious LNK / NTLM Authentication Capture
	- Library-ms


- Pass-the-Hash (PtH)
    - El hash pertenece solo al user Administrator?
    - El hash puede estar siendo reutilizado por otras cuentas?
	- Revisar en BloodHound (Shortest paths to Domain Admins, All Domain Admins) 
```
