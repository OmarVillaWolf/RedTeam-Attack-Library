# Metodología AD 

Tags: #AD #ActiveDirectory #Metodologia #Kali #Windows 

## SIN CREDENCIALES 
```bash 
## RECONOCIMIENTO

- Enumeración puerto 445 'SMB'
  	- Enumerar usuarios (--rid-brute)
	- Investigar Shares
		- Investigar carpeta de SYSVOL, NETLOGON o Personalizadas 
		- Buscar archivos con credenciales  <-  IMPORTANTE

- Enumeración puerto 135 'RPC'
	- Enumerar con nullsession en busca de usuarios
	- RID CYCLING (Si no se puede ingresar por null session)

- Enumeración puerto 88 'Kerberos'
	- Kerbrute para validar buscar usuarios válidos (BruteForce)

- Enumeración puerto 80 'web'
	- Buscar archivos con credenciales  <-  IMPORTANTE
```

```bash 
## ATAQUES 

- ASReproast Attack (Si se tiene solo el usuario sin passwd) 
```

## CON CREDENCIALES 
```bash 
## RECONOCIMIENTO 

Pasos:
- Enumeración puerto 445 'SMB'
	- Enumerar usuarios (--users)
	    - Ataque de bruteforce 'users.txt:users.txt' 
	    - Password Spraying (Misma password dada al inicio)
	- Investigar Shares (Revisar de cada usuario nuevo obtenido)
		- Investigar carpeta de SYSVOL (Common Vulnes), NETLOGON o Personalizadas 
		- Buscar archivos con credenciales  <-  IMPORTANTE 

- Verificar si el usuario dado puede ingresar por 'WinRM'

- Enumeración puerto 389/636 'LDAP'
	- Mapear toda la info con 'ldapdomaindump'

- Enumeración puerto 80 'web'
	- Buscar archivos con credenciales  <-  IMPORTANTE

- Enumeración con BloodHound 
	- Buscar Outbound Object Control (ACLs) 
	- Buscar usuarios Kerberosteables 
	- Shortest Path to Domain Admin 
	- Shortest Path from Owned objects
```

```bash 
## ATAQUES 

- Abuso de ACLs
- Kerberoasting Attack 
- SMB Writable Share -> Slinky -> Malicious LNK / NTLM Authentication Capture
- LogonScript (Dir WRITE)
- ADCS Attacks 

- Pass-the-Hash (PtH)
    - El hash pertenece solo al user Administrator?
    - El hash puede estar siendo reutilizado por otras cuentas?
	- Revisar en BloodHound (Shortest paths to Domain Admins, All Domain Admins) 
```
