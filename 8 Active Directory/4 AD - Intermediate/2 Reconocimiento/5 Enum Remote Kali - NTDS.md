# Enum Remote con Kali - NTDS.dit 

Tags: #AD #Kali #DC #NTDS 

## Ldapsearch 

```bash 
! Usuario de dominio (AD) 

❯ ldapsearch -h     # Menú de ayuda 

# Acceso anónimo si esta permitido 
❯ ldapsearch -x -H ldap://IP_DC -D '' -w '' -b 'DC=corp,DC=local'

# Consultar LDAP al Domain Controller autenticándose con el usuario corp\user1 y devuelve objetos del dominio desde el base DN DC=corp,DC=local
❯ ldapsearch -x -H ldap://IP_DC -D 'corp\user1' -w 'password' -b 'DC=corp,DC=local'
❯ ldapsearch -x -H ldap://IP_DC -D 'user1@corp.local' -w 'password' -b 'DC=corp,DC=local'

# Enumerar los usuario que se encuentran en el AD
❯ ldapsearch -x -H ldap://IP_DC -D 'corp\user1' -w 'password' -b 'CN=Users,DC=corp,DC=local' 
# Enumerar un usuario en particular en el AD
❯ ldapsearch -x -H ldap://IP_DC -D 'corp\user1' -w 'password' -b 'CN=user1,CN=Users,DC=corp,DC=local'

# Enumerar las computadoras que se encuentran en el AD
❯ ldapsearch -x -H ldap://IP_DC -D 'corp\user1' -w 'password' -b 'CN=Computers,DC=corp,DC=local'  

# Obtener la info del grupo 'Administradores' que se encuentran en el AD
❯ ldapsearch -x -H ldap://IP_DC -D 'corp\user1' -w 'password' -b 'CN=Administrators,CN=Builtin,DC=corp,DC=local'  
```
## Pywerview 

```bash 
! Usuario de dominio (AD)

❯ pywerview -h    # Menú de ayuda 

# Obtener los Domain Controllers del dominio, consultando Active Directory
❯ pywerview get-netdomiancontroller -u user --dc-ip IP -p 'password'  

# Enumerar usuarios del dominio desde Active Directory
❯ pywerview get-netuser -u user --dc-ip IP -p 'password'

# Enumerar los grupos del dominio en Active Directory.
❯ pywerview get-netgroup -u user --dc-ip IP -p 'password'

# Enumerar todas las Group Policy Objects (GPOs) del dominio desde AD
❯ pywerview get-netgpo -u user --dc-ip IP -p 'password'
```