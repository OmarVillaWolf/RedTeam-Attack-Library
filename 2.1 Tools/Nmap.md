# Nmap 

Tags: #Nmap #Escaneo #UDP #TCP 

* [NSEDocs](https://nmap.org/nsedoc/)

```bash 
❯ zenmap                           # Es la version GUI de nmap
```

## Host Discovery 

```bash 
❯ man nmap                          # Despliega el manual de nmap 
/usr/share/nmap/scripts             # Ruta que contiene los scripts de Nmap
```

```bash 
❯ nmap -sn ❮IP/24❯             # Usara 'Ping Scan' para escanear la red y descubir los diferentes dispositivos en la red
❯ nmap -sn -PR ❮IP/24❯         # Usara 'ARP' con 'Ping Scan' para escanear la red y descubrirá hosts vivos

	# sn = No escanea los puertos despues de descubir un host 'Se refiere a un escaneo de PING', es un ICMP, TCP SYNC

❯ nmap -sn -PU IP            # Escaneo UDP ping 
❯ nmap -sn -PE IP-255        # Escaneo ICMP echo 
❯ nmap -sn -PM IP-255        # Escaneo Mask ping (Se usa si el ICMP esta bloqueado)
❯ nmap -sn -PP IP-255        # Escaneo ICMP timestamp 
❯ nmap -sn -PS IP-255        # Escaneo TCP Syn ping 
❯ nmap -sn -PO IP-255        # Usa diferentes protocolos para testear la conectividad 
```
+
```bash
❯ nmap ❮IP/24❯                      # Para escanear toda la red
❯ nmap ❮IP/24❯ --reason <IP.1>      # Escanea toda la red pero trae informacion detallada de esa IP especifica 

	# Escaneo TCP Completo de los 1000 puertos mas frecuentes 
	# Protocolo usado Ping ARP y mira si el host esta activo o no
	# Target IP = El rango de direccion a escanear 1.1.1.0/24 (Debe terminar en 0 con /24)
	# reason = Ademas nos ayuda a saber que host tenemos up o down 
```

## Evadir IDS o Firewall

```bash 
# Hay un sistema de seguridad cuando al momento de escanear salen todo los puertos abiertos 
❯ nmap -p- -sS -Pn -n --disable-arp-ping --source-port 53 <IP>     # Evadir un WAF 

❯ nmap -D RND:3 -v <IP>                 # Utilizar una IP señuelo 'Decoy'
	# D RDN = Numero de 'decoy', en este caso son 3

❯ nmap -sS -Pn --spoof-mac 0 <IP>       # Falsificacion de MAC Address

❯ nmap --randomize-hosts <IP>           

❯ nmap -g 445 -v <IP>                   # Manipulacion del puerto de origen 
	# g = Puerto de origen 

❯ nmap -sS -T4 -f -v <IP>               # Fragmentamos el trafico para evadir el Firewall 'Aveces funciona'
	# f = Fragmentar 
	# sS = Aplica un TCP SYN Scan (Rapido, Fiable, Sigiloso)
	# T = Velocidad 
```

## Active Directory 

```bash 
❯ nmap -n --disable-arp-ping -p 88,389,53 --open -T5 -vvv ❮IP/24❯
```

## Port Scanning 

* [Nmap-book-scripting-engine](https://nmap.org/book/nse.html)

```bash 
❯ rustscan -a IP --ulimit 5000 -g  # Escaneo de puertos en la dirección IP
```

```bash
❯ nmap -iL <IP_File> -sV -O        # Escanear un archivo con varias IP, Version y Sistema Operativo
```

```bash 
❯ nmap -Pn <IP>
❯ nmap -Pn -p443 <IP>
❯ nmap -Pn -sV -O <IP> -oX Scan    # Importa el resultado en un archivo XML llamado 'Scan' que luego lo podemos utilizar en Metasploit 
```

```bash 
❯ nmap --top-ports 500 --open -T5 -v -n ❮Target IP❯
```

```bash 
❯ nmap -n -P0 -p- -sS -g 53 -T5 -vv ❮Target IP❯
	# n = No aplicar resolucion DNS
	# P0 = Saltar etapa de 'Host Discovery' 
	# p = Escaneo de todos los puertos '65535'
	# sS = Aplica un TCP SYN Scan (Rapido, Fiable, Sigiloso)
	# g = Puerto de origen 
	# T5 = La velocidad de escaneo 'agresivo'
	# v = Verbosidad 

❯ nmap -n -P0 -p 22,80 -sV -sS -g 53 -T5 -vv ❮Target IP❯
	# sV = Version 
```

```bash 
# Windows siempre bloquea los PING ICMP, por lo que debemos de agregar la sig. bandera
❯ nmap -Pn -F -sVC -O ❮IP/24❯ -v                
	# Pn = Identifica si el host esta activo 
	# F = Escanea los 100 puertos mas usados 
	# sV = Version 
	# O = Sistema operativo 
	# v = Verbose
	# sC = Ejecuta una lista de scipts de Nmap

```

```bash 
❯ nmap -p- --open -sS --min-rate 500 -vvv -n -Pn  ❮Target IP❯   # Escanear en un entorno real 
❯ nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn ❮Target IP❯  -oG allPorts     # Escanear

	# Protocolo usado TCP, UDP
	#  p = Escanea todos los puertos (65535)
	#  open = Muestra solo los puertos con un estatus “open”
	#  sS = Aplica un TCP SYN Scan (Rapido, Fiable, Sigiloso)
	#  min-rate 5000 = Indica que quiero emitir paquetes no más lentos que 5000 paquetes por segundo
	#  vvv = Muestra la información en pantalla a medida que se descubre
	#  n = Indica que no aplique resolución DNS
	#  Pn = Indica que no aplique el protocolo ARP
	#  Target IP = Dirección IP que se quiere escanear
	#  oG allPorts = Exporta el output a un fichero grepeable con nombre “allPorts”
	#  oX = Exporta el output a un fichero XML con nombre 'allPorts.xml' para Metasploit
```

```bash
❯ nmap -sCV -p22,... ❮Target IP❯ -oN targeted

	#  p22,... = Indica los puertos que se quieren escanear
	#  sC = Lanza scripts básicos de enumeración
	#  sV = Enumera la versión y servicio que está corriendo en los puertos
	#  Target IP = Dirección IP que se quiere escanear
	#  oN targeted = Exporta el output en formato Nmap


❯ nmap -sCV -p22,... ❮Target IP❯ -oX targeted   # Muestra el recultado en formato XML

❯ xsltproc targetedXML > index.html     # Convierte el resultado del escaneo que se obtiene por XML a HTML para poder ver los resultados desde la web 

❯ python3 -m http.server 80             # Iniciamos un servidor HTTP para ver el archivo anterior
```

```bash
❯ nmap -sU --top-ports 100 --open -T5 -v -n ❮Target IP❯     # Escaneo de puertos por UDP

	# sU = Escaneo de UDP

❯ nmap -sU -p1-250 ❮Target IP❯                 # Escaneo por UDP del 1 al 250
❯ nmap -sUV -p134,177,234 ❮Target IP❯          # Muestra las versiones de los puertos de UDP
❯ nmap -sUV -p134 ❮Target IP❯ --script=discovery    # Enumerar informacion 
```

## WordPress

```bash 
❯ nmap -sS -sV --script=http-wordpress-enum ❮IP❯     # Enumeracion de WordPress 

❯ nmap -sS -sV --script=http-wordpress-enum --script-args type="themes" ❮IP❯
❯ nmap -sS -sV --script=http-wordpress-enum --script-args type="plugins" ❮IP❯

❯ nmap -sS -sV -p80,443 --script=http-wordpress-users ❮IP❯
```

## SMB / SAMBA

```bash
❯ locate -r '\.nse$' | xargs grep categories | grep 'default\|version\|safe' | grep smb  # Filtrar los scripts por la categoria 'safe, default o version' que pertenecen al protocolo SMB

❯ nmap -p445 -sS -A ❮IP❯                   # Escaneo comprensivo 

❯ nmap -p445 -sCV ❮IP❯                     # Enumeración exaustiva al SMB 

❯ nmap -p445 --script safe ❮Target IP❯     # Ejecutar todos los scripts de Nmap que tengan la categoria 'Safe' 

❯ nmap -p445 --script='smb-vul-*' ❮IP❯     # Ejecutar todos los scripts de Nmap que empiecen con 'smb-vuln'

❯ nmap -p445 --script smb-protocols ❮Target IP❯          # Mirar los protocolos que se estan usando

❯ nmap -p445 --script smb-security-mode ❮Target IP❯      # Mirar si permite la autenticación de usuarios anonymous

❯ nmap -p445 --script smb-enum-sessions ❮Target IP❯      # Mirar si hay sesiones activas
❯ nmap -p445 --script smb-enum-sessions --script-args smbusername=administrator,smbpassword=smbserver ❮Target IP❯        
# Ver si hay sesiones activas, con un usuario y su passwd validos 'smbserver_771'

❯ nmap -p445 --script smb-enum-shares ❮Target IP❯         # Ver si hay directorios 
❯ nmap -p445 --script smb-enum-shares --script-args smbusername=administrator,smbpassword=smbserver ❮Target IP❯          
# Ver si hay directorios con un usuario y passwd validos 

❯ nmap -p445 --script smb-enum-users ❮Target IP❯          # Ver si hay usuarios 
❯ nmap -p445 --script smb-enum-users --script-args smbusername=administrator,smbpassword=smbserver ❮IP❯          
# Ver si hay usuarios con un usuario y passwd validos 

❯ nmap -p445 --script smb-server-stats --script-args smbusername=administrator,smbpassword=smbserver ❮Target IP❯
# Ver las estadisticas del servidor, se observa cuanta data es enviada y recibida

❯ nmap -p445 --script smb-enum-domains --script-args smbusername=administrator,smbpassword=smbserver ❮Target IP❯
# Ver los dominios existentes

❯ nmap -p445 --script smb-enum-groups --script-args smbusername=administrator,smbpassword=smbserver ❮Target IP❯
# Ver los grupos existentes 

❯ nmap -p445 --script smb-enum-services ❮Target IP❯ -d 
❯ nmap -p445 --script smb-enum-services --script-args smbusername=administrator,smbpassword=smbserver ❮Target IP❯
# Mirar los servicios que estan corriendo

❯ nmap -p445 --script smb-enum-shares,smb-ls --script-args smbusername=administrator,smbpassword=smbserver ❮Target IP❯
# Conectarse al SMB y ejecutar 'ls'

❯ nmap -p445 --script smb-os-discovery ❮Target IP❯    # Mirar la versión del SO, FQDN, Domain, NetBIOS, Computer name, System time
```

```bash 
# Hay puertos por UDP que pertenecen al Samba como 137,138

❯ nmap ❮Target IP❯ -sU --top-port 25 --open             # Escaneo de puertos UDP, encontramos el 137,138
❯ nmap ❮Target IP❯ -sU --top-port 25 --open -sV         # Mirar la version de los puertos encontrados para SMB
```

## EternalBlue 'MS17-010' Windows7

```bash 
❯ nmap -sV -p445 --script=smb-vuln-ms17-010 ❮Target IP❯             # Para ver si es vulnerable al EternalBlue

❯ nmap --script "vuln and safe" -p445 ❮Target IP❯ -oN smbVulnScan   # Para ver si este servicio es vulnerable al EternalBlue (ms17-010) en Windows 7.

	#  p445 = Indica el puerto que se quiere escanear
	#  Target IP = Dirección IP que se quiere escanear
	#  oN smbVulnScan = Exporta el output a un fichero en formato nmap con nombre “smbVulnScan”
	#  vuln and safe = Detecta vulnerabilidades de forma segura, sin experimentar un DoS
```

## HTTP

```bash
❯ nmap --script http-enum -p80 ❮Target IP❯ -oN WebScan #  http-enum = Aplica Fuzing a HTTP, utiliza un diccionario de 1000 rutas y ver si hay algunas rutas existen

	#  -p80 = Indica el puerto que se quiere escanear
	#  Target IP = Dirección IP que se quiere escanear
	#  -oN WebScan = Exporta el output a un fichero en formato nmap con nombre “WebScan”

# Enumeración WampServer
❯ nmap -p 80, 8080, 3306 -sV ❮Target IP❯    # El output debe mostrar el titulo de 'http-title WampServer' en un Apache, además de usar PHP y tener el puerto 3306 de MySQL 'Web phpMyAdmin'
❯ nmap --script http-tittle,http-server-header -p 80, 8080 ❮Target IP❯

# Enumeracion de IIS
❯ nmap --script http-headers -p80 ❮Target IP❯         # Nos trae informacion sobre las cabeceras

# Muestra información sobre los métodos soportados
❯ nmap --script http-methods -p80 ❮Target IP❯ --script-args http-methods.url-path=/webdav/      

# Identifica las instalaciones de Webdav, utilizando opciones y metodos
❯ nmap --script http-webdav-scan -p80 ❮Target IP❯ --script-args http-methods.url-path=/webdav/ 

# Enumeracion de Apache
❯ nmap --script banner -p80 ❮Target IP❯    # Miras el banner y su informacion principal 
```

## HTTPS

```bash
❯ nmap --script ssl-heartbleed -p443 ❮Target IP❯ 	# heartbleed = Verifica si es vulnerable a Heartbleed

	#  -p8443 o 443 = Indica el puerto que se quiere escanear
	#  Target IP = Dirección IP que se quiere escanear
```

## FTP

```bash
❯ nmap --script ftp-anon -p21 ❮Target IP❯              # ftp-enum = Escanea y mira si el usuario invitado 'Anonymous' esta habilitado

	#  p21 = Indica el puerto que se quiere escanear
	#  Target IP = Dirección IP que se quiere escanear


# Cuando tenemos un usuario valido podemos hacerle fuerza bruta, colocamos la ruta del archivo que contiene al usuario
❯ nmap ❮Target IP❯ --script ftp-brute --script-args userdb=/root/users.txt -p21
```

## SSH

```bash 
❯ nmap --script ssh2-enum-algos -p22 ❮Target IP❯     # Enumeracion del SSH, algos = enumera todos los algoritmos

❯ nmap --script ssh-hostkey -p22 ❮Target IP❯ --script-args ssh_hostkey=full   # Nos muestra todos los SSH RSA host key

❯ nmap --script ssh-auth-methods -p22 ❮Target IP❯ --script-args="ssh.user=<username>" # Para saber si el usuario tiene metodos de autenticacion como 'publickey, password', de lo contrario podriamos entrar sin passwd

# Cuando tenemos un usuario valido podemos hacerle fuerza bruta, colocamos la ruta del archivo que contiene al usuario
❯ nmap ❮Target IP❯ --script ssh-brute --script-args userdb=/root/users.txt -p22
```

## SNMP

```bash 
❯ nmap -sU -p 161 --script=snmp-processes IP    # Enumerar los procesos que se estan ejecutando 

❯ nmap -sU --script snmp-brute IP [--scripts-args snmp-brute.communitiesdb=rockyou.txt] # Ataque de diccionario 

❯ nmap -sU -p 161 --script=snmp-interfaces IP   # Muestra las interfaces del servidor 
```

## NetBIOS

```bash 
❯ nmap -sV -v --script nbstat.nse IP 
❯ nmap -sU -p 137 --script nbstat.nse IP 
```

## NFS

```bash 
❯ nmap -sV --script=nfs-showmount IP 
```

## MYSQL

```bash 
❯ nmap ❮Target IP❯ -p 3306 --script=mysql-empty-password     # Nos muestra los usuarios que se pueden loggear sin password 'anonymous login'

❯ nmap ❮Target IP❯ -p 3306 --script=mysql-info               # Nos muestra informacion de la base de datos, capabilities 

# Colocamos un usuario y su passwd valida, si somos root nos mostrara los usuarios existentes en la DB
❯ nmap ❮Target IP❯ -p 3306 --script=mysql-users --script-args="mysqluser='root',mysqlpass=''" 

# Colocamos un usuario y su passwd valida, si somos root nos mostrara las bases de datos en la DB
❯ nmap ❮Target IP❯ -p 3306 --script=mysql-databases --script-args="mysqluser='root',mysqlpass=''"

# Colocamos un usuario y su passwd valida, si somos root nos mostrara el directorio 'datadir:/var/lib/mysql/'
❯ nmap ❮Target IP❯ -p 3306 --script=mysql-variables --script-args="mysqluser='root',mysqlpass=''"

# Colocamos un usuario y su passwd valida, Si el archivo de privilegios puede ser otorgado a usuarios no admin
❯ nmap ❮Target IP❯ -p 3306 --script=mysql-audit --script-args="mysql-audit.username='root',mysql-audit.password='',mysql-audit.filename='/usr/share/nmap/nselib/data/mysql-cis.audit'"

# Colocamos un usuario y su passwd valida, nos muestra los hashes de los usuarios 
❯ nmap ❮Target IP❯ -p 3306 --script=mysql-dump-hashes --script-args="username='root',password=''"

# Colocamos un usuario y su passwd valida, y asi podremos usar una query para que nos muestre 
❯ nmap ❮Target IP❯ -p 3306 --script=mysql-query --script-args="query='select count(*) from <dbs.table_name>;',username='root',password=''"
```

## MSSQL

```bash 
❯ nmap ❮Target IP❯ -p 1433 --script ms-sql-info     # Nos muestra la informacion de la DB

❯ nmap ❮Target IP❯ -p 1433 --script ms-sql-ntlm-info --script-args mssql.instance-port=1433   # Nos muestra la informacion de la DB

# Hacemos fuerza bruta para enumerar usuarios y passwds
❯ nmap ❮Target IP❯ -p 1433 --script ms-sql-brute --script-args userdb=/root/Descktop/wordlists/common_users.txt,passdb=/root/Desktop/wordlists/100-common-passwords.txt

❯ nmap ❮Target IP❯ -p 1433 --script ms-sql-empty-password     # Nos muestra los usuarios que se pueden loggear sin password 'anonymous login' o 'sa user'

# Colocamos un usuario y su passwd valida, y asi podremos usar una query para qextraer los 'sysusers'
❯ nmap ❮Target IP❯ -p 1433 --script ms-sql-query --script-args mssql.username=admin,mssql.password=<password>,ms-sql-query.query="SELECT * FROM <db>.<tablename>" -oN out.txt

	# out.txt = Formato de salida del escaneo de Nmap 

❯ nmap ❮Target IP❯ -p 1433 --script ms-sql-dump-hashes --script-args mssql.username=admin,mssql.password=<passwd> # Nos muestra los hashes

❯ nmap ❮Target IP❯ -p 1433 --script ms-sql-xp-cmdshell --script-args mssql.username=admin,mssql.password=<passwd>,ms-sql-xp-cmdshell.cmd="ipconfig" # Podemos ejecutar comandos de forma remota usando la base de datos
	# Podemos usar mas comandos como: 'type C:\flag.txt' y nos mostrara el contenido de la flag por consola
```

## LDAP
```bash
❯ nmap --script ldap\* -p389 ❮Target IP❯               # Para enumerar LDAP

	#  p389 = Indica el puerto que se quiere escanear
	#  Target IP = Dirección IP que se quiere escanear
```

## Shellshock
```bash 
# Ver si es vulnerable a ShellShock, pero antes debemos de buscar la ruta en donde se encuentra el cgi

❯ nmap -p80 <IP> --script=http-shellshock --script-args "http-shelshock.uri=/gettime.cgi" 
❯ nmap --script http-shellshock --script-args uri=/cgi-bin/user.sh -p80 <IP>
```

## RPC
```bash 
❯ nmap -p111 --script=nfs-ls,nfs-statfs,nfs-showmount <IP>   # Enumerar el servicio RPC, podemos ver las monturas e informacion adicional  
```

## Opciones de Nmap

```python
# SYN        -> TCP envia 1 paquete 
# ACK        -> Confirmacion
# FIN        -> Origen (acaba)
# RST        -> Error (Politica de seguridad) no permite ICMP (Ping) -  IP Tables - Logs
# PSH        -> No se almacena en un Buffer, los paquetes que llegan los va procesando (Cache - Running - Real Time Apps)
# URG        -> Macht a x segmentos y da prioridad


# OPEN       -> Podemos interactuar con ese puerto abierto (Servicio)
# CLOSED     -> Nmap con el TWH muestra el puerto cerrado (politicas), pero con tecnicas de evasion podriamos vulnerarlo
# FILTERED   -> Existe una herramienta de seguridad intermediaria (FW, IDS, IPS) que filtra los paquetes, reglas de ruteo o que el puerto es de un host basado en un software de Firewall
# OPEN FILTERED -> Nmap no sabe si el puerto esta abierto o filtrado, cuando envia el SYN lo detecta y detecta una herramienta 
# CLOSED FILTERED -> Nmap no sabe si esta cerrado, filtrado o lo estan usando para escaneo de la IP


# TCP CONNECT SCAN - Tecnica completa de Scan - SYN Scan, puede ser detectado por los FW
# SCAN SYN - Abreviado (SYN - SYN ACK - RST) - Silencioso
❯ nmap <IP>   # Aqui si se completa la sesion en el Three Way Handshake (Demorado, evidencia)

# UDP Scan - Escaneo lento
-sU          # Escaneo por UDP (DHCP - 67 servidor / 68 Cliente)

# NULL, FIN, XMAS Scan - paquetes = 0 - Para evasion de Firewall, ya que los Firewall no examinan paquetes con flag apagados
-sN          # Evasion de Firewall o IDS
-sX          # Evasion de Firewall un XMAS
-sF          # Escaneo NULL, escaneo FIN (Inicie el escaneo y finalicelo), cuando queremos engañar HonneyPots

-sT          # Sirve para cerrar la conexion (Menos eficiente en el escaneo), sirve para el Firewall 
-sW          # Estudia el Firewall de Windows (Mira la sintaxis, politicas, etc... bloquea), trata de alcanzar los puertos y conectar (Tarda mucho tiempo)

# Flags
-A           # Escaneo agresivo, traer mas informacion ya que combina -O, -sC, -sV
-sA          # Escaneo tipo TCP ACK, forzara a que el destino envie el ACK (Agresivo)
-O           # Sistema Operativo
-v           # Verbose, cada que encuentre algo que nos lo reporte por consola, podemos tener hasta (-vvv)
-n           # Evita la resolucion de DNS inverso por ende, aceleras el proceso
-sS          # Aplican un escaneo TCP SYN Scan (Rapido, Fiable, Sigiloso), pero no se completa la session de TWH
-sT          # Sirve para cerrar la conexion (Menos eficiente en el escaneo), sirve para el Firewall 
-p           # Escaneo a puertos, -p- Todos los puertos, -p-65535, -p 1-500 Ciertos puertos
-T4          # Temporizador (0, 1, 2, 3, 4, 5) la velocidad el escaneo
-PE          # Peticiones ICMP de modo 'echo', solo se envia cuando haga ping a un host 
-PP          # Utiliza peticiones tipe stand, nos trae las librerias que tengan esos puertos, aveces trae algoritmos criptograficos
-PM          # 
-PS          # Enviamos paquetes con el bit de control SYN y que nos diga si hay o no respuesta 
```