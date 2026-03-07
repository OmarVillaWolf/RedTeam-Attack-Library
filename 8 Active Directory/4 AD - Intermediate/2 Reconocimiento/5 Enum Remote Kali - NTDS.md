# Enum Remote con Kali - NTDS.dit 

Tags: #AD #Kali #DC #NTDS 

## Ldapsearch 

```bash 
# Usar credenciales validas 

❯ ldapsearch -h     # Menú de ayuda 

❯ ldapsearch -x -h IP_DC -D '' -w '' -b 'DC=corp,DC=local'    # Acceso anónimo 
# Enumerar todos los objetos que se encuentran en 'corp.local'
❯ ldapsearch -x -h IP_DC -D 'corp\user1' -w 'password' -b 'DC=corp,DC=local' 
# Enumerar los usuario que se encuentran en 'corp.local'
❯ ldapsearch -x -h IP_DC -D 'corp\user1' -w 'password' -b 'CN=Users,DC=corp,DC=local' 
# Enumerar un usuario en particular en 'corp.local'
❯ ldapsearch -x -h IP_DC -D 'corp\user1' -w 'password' -b 'CN=user1,CN=Users,DC=corp,DC=local'
# Enumerar las computadoras que se encuentran en 'corp.local'
❯ ldapsearch -x -h IP_DC -D 'corp\user1' -w 'password' -b 'CN=Computers,DC=corp,DC=local'  
# Obtener la info del grupo 'Administradores' que se encuentra en 'corp.local'
❯ ldapsearch -x -h IP_DC -D 'corp\user1' -w 'password' -b 'CN=Administrators,CN=Builtin,DC=corp,DC=local'  
```
## Pywerview 

```bash 
# Usar credenciales validas 

❯ pywerview -h    # Menú de ayuda 

❯ pywerview get-netdomiancontroller -u user --dc-ip IP -p password   # Info del DC 
❯ pywerview get-netuser -u user --dc-ip IP -p password     # Obtener los usuarios 
❯ pywerview get-netgroup -u user --dc-ip IP -p password    # Obtener los grupos 
❯ pywerview get-netgpo -u user --dc-ip IP -p password      # Obtener las GPOs
```

## Jxplorer

```bash 
# Usar credenciales validas 

❯ jxplorer       # Tool para intercatuar con LDAP desde una interfaz gráfica 
```