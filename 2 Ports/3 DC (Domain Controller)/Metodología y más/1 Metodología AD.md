# Metodología AD 

Tags: #AD #ActiveDirectory #Metodologia #Kali #Windows 

## SIN CREDENCIALES 
```bash 
- Enumeración puerto 445 'SMB'
  	- Enumerar usuarios (--rid-brute)
	- Investigar Shares
		- Investigar carpeta de SYSVOL, NETLOGON o Personalizadas 
		- Buscar archivos con credenciales 

- Enumeración puerto 135 'RPC'
	- Enumerar con nullsession en busca de usuarios
	- RID CYCLING (Si no se puede ingresar por null session)

- Enumeración puerto 88 'Kerberos'
	- Kerbrute para validar buscar usuarios válidos (BruteForce)

- ASReproast Attack (Si se tiene solo el usuario sin passwd) 
```

## CON CREDENCIALES 
```bash 
Pasos:
- Enumeración puerto 445 'SMB'
	- Enumerar usuarios (--users)
	    - Ataque de bruteforce 'users.txt:users.txt' 
	    - Password Spraying (Misma password dada al inicio)
	- Investigar Shares
		- Investigar carpeta de SYSVOL, NETLOGON o Personalizadas 
		- Buscar archivos con credenciales 

- Verificar si el usuario dado puede ingresar por 'WinRM'

- Enumeración puerto 389/636 'LDAP'
	- Mapear toda la info con 'ldapdomaindump'

- Kerberoasting Attack

- Enumeración con BloodHound 
	- Abuso de ACLs 
```
