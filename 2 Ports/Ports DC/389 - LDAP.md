# LDAP

Tags: #LDAP #OWASP #Explotacion #DC #Impacket 

* [LDAP](https://www.profesionalreview.com/2019/01/05/ldap/)

```bash 
LDAP es un protocolo estándar diseñado para mantener y acceder a los servicios de directorio dentro de una red. Piense en un servicio de directorio como una agenda telefónica para diferentes recursos de red, como archivos, impresoras, usuarios, dispositivos, servidores, etc.
```

Las inyecciones **LDAP** (**Protocolo de Directorio Ligero**) son un tipo de ataque en el que se aprovechan las vulnerabilidades en las aplicaciones web que interactúan con un servidor LDAP. El servidor LDAP es un directorio que se utiliza para almacenar información de usuarios y recursos en una red.

La inyección LDAP funciona mediante la inserción de comandos LDAP maliciosos en los campos de entrada de una aplicación web, que luego son enviados al servidor LDAP para su procesamiento. Si la aplicación web no está diseñada adecuadamente para manejar la entrada del usuario, un atacante puede aprovechar esta debilidad para realizar operaciones no autorizadas en el servidor LDAP.

Al igual que las inyecciones SQL y NoSQL, las inyecciones LDAP pueden ser muy peligrosas. Algunos ejemplos de lo que un atacante podría lograr mediante una inyección LDAP incluyen:

```bash 
-   Acceder a información de usuarios o recursos que no debería tener acceso.
-   Realizar cambios no autorizados en la base de datos del servidor LDAP, como agregar o eliminar usuarios o recursos.
-   Realizar operaciones maliciosas en la red, como lanzar ataques de phishing o instalar software malicioso en los sistemas de la red.
```

Para evitar las inyecciones LDAP, las aplicaciones web que interactúan con un servidor LDAP deben validar y limpiar adecuadamente la entrada del usuario antes de enviarla al servidor LDAP. Esto incluye la validación de la sintaxis de los campos de entrada, la eliminación de caracteres especiales y la limitación de los comandos que pueden ser ejecutados en el servidor LDAP.

También es importante que las aplicaciones web se ejecuten con privilegios mínimos en la red y que se monitoreen regularmente las actividades del servidor LDAP para detectar posibles inyecciones.

A continuación, se proporciona el enlace directo al proyecto de GitHub que nos descargamos para desplegar un laboratorio práctico donde poder ejecutar esta vulnerabilidad:

-   **LDAP-Injection-Vuln-App**: [https://github.com/motikan2010/LDAP-Injection-Vuln-App](https://github.com/motikan2010/LDAP-Injection-Vuln-App)

Este es el puerto estándar para LDAP (Lightweight Directory Access Protocol), que se utiliza para acceder y gestionar los servicios de directorio. En el contexto de AD, LDAP se usa para realizar consultas y cambios en el directorio de AD.

## Impacket-GetADUsers 

```bash 
❯ impacket-GetADUsers -all domain.com/user -dc-ip IP     # Enumeración de usuarios en el dominio con un usuario valido 
```

## LDAP

* [LDAP-Pentesting](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-ldap.html#ldapsearch)

```bash 
❯ ldapdomaindump -u 'domain1.corp\user' -p 'password' IP      # Crear archivos para poder ver la info desde la web
❯ ldapdomaindump -u 'domain1.corp\user' -p 'password' -o ldd ldap://IP
	# ldd = Directorio en donde se exportará la info 

	❯ python3 -m http.server 80                       # Montar un server para mirar los archivos 
```

```bash 
❯ ldapsearch -x -H <IP> -s base namingcontexts         # Enumerar con una autenticación simple
❯ ldapsearch -x -H <IP> -b 'DC=Domain.corp'            # Enumerar el dominio 


❯ ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=Domain,dc=corp" -w admin 'cn=admin'   # Mirar la información del usuario admin 

	# b,dc,dc = Base de busqueda
	# D = Nombre distinguido
	# 1er 'cn' = Usuario a autenticar
	# w = Password 
	# x = Autenticacion simple (Usuario, Passwd)
	# 2do 'cn' = Criterio de busqueda y si existe muestra más info  


# Enumerar un usuario en especifico 
❯ ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=Domain,dc=org" -w admin 'cn=omar'


❯ ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=Domain,dc=org" -w admin '(&(cn=omar)(description=LDAP*))'

	# (&(cn=omar)(description=LDAP*)) = El usuario a buscar es 'omar' y su descripción es LDAP 
	# * = Mostrar más contenido 
	# (|(cn=omar)(description=LDAP*)) = El usuario a buscar es 'omar' o su descripción es LDAP
```

```bash
# Crea un usuario en base a un archivo dado 
❯ ldapadd -x -H ldap://localhost -D "cn=admin,dc=example,dc=org" -w admin -f newuser.ldif  
```