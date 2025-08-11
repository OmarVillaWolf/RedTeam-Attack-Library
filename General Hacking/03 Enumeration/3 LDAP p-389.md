# Enumeración de LDAP 

Tags: #Hacking #Enumeration #LDAP #DC 

## LDAPSearch

```bash 
# Kali Tool 
❯ ldapsearch -h IP -x -s base namingcontexts 
	# x = Autenticación simple 
	# h = Host 
	# s = Scope  

❯ ldapsearch -h IP -x -b "DC=domain,DC=com" 

❯ ldapsearch -x -h IP -b "DC=domain,DC=com" "objectclass=*"

❯ ldapsearch -x -h IP -b "DC=domain,DC=com" "objectclass=user" enm    # Identificar las cuentas de usuarios asociadas al dominio, no se cuentan los que no tengan 'CN=Users', ademas muestra la versión de LDAP
```

## AD Explorer 

```bash 
# Windows Tool 
❯ ADExplorer.exe     # Ejecutamos el programa 

Nota: 
	1. Saldrá un cuadro en donde se debe colocar el server, asi como su usaurio y contraseña si existe 
	2. Se puede ver la info desplegando los '+' como el de 'CN=Users' 
```

```bash 
# Más Tools 
❯ https://www.ldapadministrator.com 
❯ https://www.ldapsoft.com
❯ https://www.ldap-account-manager.org
❯ https://securityxploded.com 
```

## Nmap y Python3

```bash 
# Kali Tool 
❯ nmap -sU -p 389 IP    
❯ nmap -p 389 --script ldap-brute --script-args ldap.base='"cn=users,dc=domain,dc=com"' IP 


❯ python3 
	❯ import ldap3 
	❯ server=ldap3.Server('IP_Server', get_info=ldap3.ALL, port=389)
	❯ connection=ldap3.Connection(server)
	❯ connection.bind()    # Debe mostrar un 'True'
	❯ server.info          # Muestra la info del server 
	
	❯ connecton.search(search_base='DC=domain,DC=com', search_filter='(&(objectclass=*))', search_scope='SUBTREE', attributes='*')   # Debe mostrar un 'True'
	❯ connection.entries   # Muestra todos los objetos de directorio 

	❯ connecton.search(search_base='DC=domain,DC=com', search_filter='(&(objectclass=person))', search_scope='SUBTREE', attributes='userpassword')   # Debe mostrar un 'True'
	❯ connection.entries   # Muestra los atributos de usuarios 
```

