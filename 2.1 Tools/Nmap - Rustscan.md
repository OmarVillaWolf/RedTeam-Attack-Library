# Reconocimiento de Red y Puertos

Tags: #Nmap #RustScan #Reconocimiento #Escaneo #TCP #UDP #NSE #Scripts

## OBJETIVO
- Descubrir hosts activos en la red
- Identificar puertos abiertos y servicios corriendo
- Detectar versiones, OS y banners
- Ejecutar scripts NSE para enumeración y detección de vulnerabilidades
- Evadir firewalls e IDS cuando sea necesario

## TIPS
1. **Flujo siempre en dos pasos: descubrir puertos rápido → luego versión + scripts en los encontrados**
2. **RustScan para descubrir puertos → nmap para enumerar servicios → son complementarios**
3. **-sS (SYN scan) → más rápido y sigiloso → requiere root**
4. **-Pn → Windows casi siempre bloquea ICMP → agregar si no responde al ping**
5. **--min-rate 5000 → laboratorios | --min-rate 500 → entornos reales**
6. **-n → no resolver DNS → acelera considerablemente el escaneo**
7. **UDP es lento → escanear solo los puertos más comunes**
8. **-oA → guardar en los 3 formatos a la vez → siempre hacerlo**

## RECURSOS
* [NSEDocs](https://nmap.org/nsedoc/) → documentación de todos los scripts NSE
* Zenmap → versión GUI de nmap

```bash
/usr/share/nmap/scripts    # Ruta de todos los scripts NSE disponibles
```

---

## FLUJO RECOMENDADO EN EL EXAMEN

```bash
# PASO 1 → Descubrir todos los puertos abiertos (elegir uno)

# Opción A — RustScan (más rápido)
❯ rustscan -a <IP> --ulimit 5000 -g
# --ulimit 5000 → necesario para máxima velocidad | -g → grepeable

# Opción B — Nmap
❯ nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn <IP> -oG allPorts
# -p- → todos los 65535 puertos | --open → solo abiertos
# -sS → SYN scan | --min-rate 5000 → velocidad alta

# PASO 2 → Versión y scripts en los puertos encontrados
❯ nmap -sCV -p<puertos> <IP> -oN targeted
# -sC → scripts por defecto | -sV → versión | -oN → output legible

# PASO 3 → UDP en los más comunes (si aplica)
❯ nmap -sU --top-ports 100 --open -T5 -v -n <IP>
```

---

## 1. DESCUBRIMIENTO DE HOSTS

```bash
# Descubrir hosts activos en una red
❯ nmap -sn <IP/24>
# Ping scan → ICMP + TCP → sin escanear puertos

❯ nmap -sn -PR <IP/24>
# ARP ping → más fiable en redes locales → detecta hosts que bloquean ICMP

❯ nmap -sn -PS <IP>-255
# TCP SYN ping → fiable en hosts que bloquean ICMP

❯ nmap -sn -PE <IP>-255
# ICMP echo → ping clásico

❯ nmap -sn -PM <IP>-255
# ICMP mask ping → útil si ICMP echo está bloqueado

❯ nmap -sn -PP <IP>-255
# ICMP timestamp

❯ nmap -sn -PU <IP>
# UDP ping scan

❯ nmap -sn -PO <IP>-255
# Múltiples protocolos para testear conectividad

❯ nmap <IP/24>
# Escaneo básico de toda la red → top 1000 puertos

❯ nmap <IP/24> --reason <IP>
# Información detallada de host específico + razón del estado

# Identificar DCs en la red
❯ nmap -n --disable-arp-ping -p 88,389,53 --open -T5 -vvv <IP/24>
# Puertos Kerberos, LDAP, DNS → confirman DC
```

---

## 2. DESCUBRIMIENTO DE PUERTOS ABIERTOS

```bash
❯ nmap -p- --open -sS -n -Pn -vvv --scan-delay 3 --min-rate 300 <IP> -oG allPorts

# RustScan → más rápido que nmap para descubrir puertos
❯ rustscan -a <IP> --ulimit 5000
# Descubrimiento básico → lista puertos abiertos

❯ rustscan -a <IP> --ulimit 5000 -g
# -g → formato grepeable → para parsear

❯ rustscan -a <IP> -p 1-65535 --ulimit 5000
# Especificar rango explícitamente

❯ rustscan -a <IP/24> --ulimit 5000
# Escanear toda una subred

# Nmap → todos los puertos TCP
❯ nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn <IP> -oG allPorts
# Laboratorios → velocidad alta

❯ nmap -p- --open -sS --min-rate 500 -vvv -n -Pn <IP> -oG allPorts
# Entornos reales → más cuidadoso

❯ nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn <IP> -oA allPorts
# -oA → guardar en los 3 formatos a la vez

❯ nmap -n -P0 -p- -sS -g 53 -T5 -vv <IP>
# -g 53 → usar puerto origen 53 → evadir algunos firewalls
# -P0 → saltar host discovery

❯ nmap --top-ports 500 --open -T5 -v -n <IP>
# Top 500 puertos más comunes → más rápido que -p-

❯ nmap -Pn -F -sVC -O <IP> -v
# -F → top 100 puertos | rápido para Windows
```

---

## 3. ENUMERACIÓN DE SERVICIOS Y VERSIONES

```bash
# Segundo escaneo → solo en puertos encontrados en el paso anterior

❯ nmap -sCV -p22,80,443 <IP> -oN targeted
# -sC → scripts por defecto | -sV → versión | -oN → output legible
# → el más usado en el examen

❯ nmap -sCV -p22,80,443 <IP> -oX targeted.xml
# -oX → formato XML → importar en Metasploit

❯ nmap -sCV -p22,80,443 <IP> -oA targeted
# -oA → los 3 formatos a la vez → recomendado siempre

❯ nmap -iL <IP_File> -sV -O
# Escanear lista de IPs desde archivo

❯ nmap -Pn -sV -O <IP> -oX Scan
# -O → detección de OS | -oX → XML para Metasploit

# Pasar resultado de RustScan directamente a nmap
❯ rustscan -a <IP> --ulimit 5000 -- -sCV -oN targeted
# Todo después de -- son flags de nmap
# RustScan descubre → nmap enumera → el mejor flujo combinado

❯ rustscan -a <IP> --ulimit 5000 -- -sCV -oA targeted
# Mismo pero guardando en todos los formatos

# Convertir resultado XML a HTML para visualizar
❯ xsltproc targeted.xml > targeted.html
❯ python3 -m http.server 80
# Ver en navegador → más cómodo para leer resultados largos
```

---

## 4. ESCANEO UDP

```bash
❯ nmap -sU --top-ports 100 --open -T5 -v -n <IP>
# Top 100 UDP más comunes → balance velocidad/cobertura

❯ nmap -sU -p 53,69,123,161,500 <IP>
# Puertos UDP clave: DNS, TFTP, NTP, SNMP, IPSec

❯ nmap -sU -p1-250 <IP>
# UDP del 1 al 250

❯ nmap -sUV -p 69,161,162 <IP>
# Versión de puertos UDP específicos

❯ nmap -sUV -p161 <IP> --script=discovery
# Enumerar información SNMP con detección de versión

# UDP junto con TCP en un solo escaneo
❯ nmap -sS -sU -p T:22,80,443,U:53,161 <IP>
# T: → puertos TCP | U: → puertos UDP
```

### Puertos UDP más importantes
```
53  → DNS
69  → TFTP (sin autenticación)
123 → NTP
161 → SNMP
500 → IPSec/IKE
```

---

## 5. EVASIÓN DE FIREWALL / IDS

```bash
❯ nmap -p- -sS -Pn -n --disable-arp-ping --source-port 53 <IP>
# --source-port 53 → simular tráfico DNS → evadir algunos WAF

❯ nmap -D RND:3 -v <IP>
# -D RND:3 → 3 IPs señuelo (decoy) → ocultar IP real

❯ nmap -sS -Pn --spoof-mac 0 <IP>
# --spoof-mac 0 → falsificar MAC address aleatoria

❯ nmap --randomize-hosts <IP>
# Aleatorizar orden de escaneo

❯ nmap -g 445 -v <IP>
# -g → puerto de origen → simular tráfico SMB

❯ nmap -sS -T4 -f -v <IP>
# -f → fragmentar paquetes → evadir inspección profunda

❯ nmap -sN <IP>
# NULL scan → sin flags TCP → evadir algunos firewalls

❯ nmap -sX <IP>
# XMAS scan → FIN+PSH+URG → evadir algunos IDS

❯ nmap -sF <IP>
# FIN scan → engañar honeypots

❯ nmap -sA <IP>
# ACK scan → mapear reglas del firewall (no detecta servicios)
```

---

## 6. SCRIPTS NSE POR SERVICIO

### SMB (445)
```bash
❯ nmap -Pn -script=smb-vuln\* -p445 <IP>     # -- MEJOR -- Detecta si tiene alguna vulnerabilidad y muestra su CVE 

❯ nmap -Pn -script=smb-protocols,smb2-security-mode,smb-os-discovery,smb-enum-shares -p445 <IP> -v 
# Escaneo más profundo 

❯ nmap -p445 -sCV <IP>
# Enumeración exhaustiva de SMB

❯ nmap -p445 --script safe <IP>
# Todos los scripts "safe" → sin riesgo de DoS

❯ nmap -p445 --script='smb-vuln-*' <IP>
# Todos los scripts de vulnerabilidades SMB

❯ nmap -p445 --script smb-protocols <IP>
# Versiones SMB habilitadas → detectar SMBv1

❯ nmap -p445 --script smb-security-mode <IP>
# Permite autenticación anónima

❯ nmap -p445 --script smb-enum-sessions <IP>
# Sesiones activas sin creds

❯ nmap -p445 --script smb-enum-sessions   --script-args smbusername=administrator,smbpassword=smbserver <IP>
# Sesiones activas con creds

❯ nmap -p445 --script smb-enum-shares <IP>
# Shares disponibles

❯ nmap -p445 --script smb-enum-shares   --script-args smbusername=administrator,smbpassword=smbserver <IP>
# Shares con creds

❯ nmap -p445 --script smb-enum-users <IP>
# Usuarios del dominio

❯ nmap -p445 --script smb-enum-users   --script-args smbusername=administrator,smbpassword=smbserver <IP>

❯ nmap -p445 --script smb-enum-domains   --script-args smbusername=administrator,smbpassword=smbserver <IP>
# Dominios existentes

❯ nmap -p445 --script smb-enum-groups   --script-args smbusername=administrator,smbpassword=smbserver <IP>
# Grupos del dominio

❯ nmap -p445 --script smb-server-stats   --script-args smbusername=administrator,smbpassword=smbserver <IP>
# Estadísticas del servidor

❯ nmap -p445 --script smb-enum-services   --script-args smbusername=administrator,smbpassword=smbserver <IP>
# Servicios corriendo

❯ nmap -p445 --script smb-enum-shares,smb-ls   --script-args smbusername=administrator,smbpassword=smbserver <IP>
# Shares + listar contenido

❯ nmap -p445 --script smb-os-discovery <IP>
# OS, FQDN, dominio, NetBIOS, hostname, tiempo del sistema

❯ locate -r '\.nse$' | xargs grep categories | grep 'default\|version\|safe' | grep smb
# Filtrar scripts SMB disponibles por categoría
```

### EternalBlue — MS17-010
```bash
❯ nmap -sV -p445 --script=smb-vuln-ms17-010 <IP>
# Verificar si es vulnerable a EternalBlue

❯ nmap --script "vuln and safe" -p445 <IP> -oN smbVulnScan
# Detectar vulnerabilidades SMB de forma segura
```

### HTTP (80 / 443 / 8080)
```bash
❯ nmap --script http-enum -p80 <IP> -oN WebScan
# Fuzzing básico → 1000 rutas comunes

❯ nmap --script http-headers -p80 <IP>
# Cabeceras HTTP → tecnologías y versiones

❯ nmap --script http-methods -p80 <IP>   --script-args http-methods.url-path=/webdav/
# Métodos HTTP permitidos en ruta específica

❯ nmap --script http-webdav-scan -p80 <IP>   --script-args http-methods.url-path=/webdav/
# Identificar WebDAV

❯ nmap --script banner -p80 <IP>
# Banner del servidor

❯ nmap --script http-title,http-server-header -p80,8080 <IP>
# Título de la página y cabecera del servidor

❯ nmap -p80,8080,3306 -sV <IP>
# Enumeración WampServer → Apache + PHP + MySQL

# IIS
❯ nmap --script http-iis-webdav-vuln -p80,8080 <IP>
# Vulnerabilidades IIS WebDAV

❯ nmap --script http-auth-finder -p80 <IP>
# Identificar métodos de autenticación HTTP
```

### HTTPS (443)
```bash
❯ nmap --script ssl-heartbleed -p443 <IP>
# Heartbleed (CVE-2014-0160)

❯ nmap --script ssl-cert -p443 <IP>
# Certificado SSL → organización, dominio, fechas

❯ nmap --script ssl-enum-ciphers -p443 <IP>
# Cifrados SSL/TLS soportados → detectar cifrados débiles

❯ nmap --script ssl-poodle -p443 <IP>
# Vulnerabilidad POODLE (SSLv3)

❯ nmap --script ssl-dh-params -p443 <IP>
# Parámetros Diffie-Hellman → detectar Logjam
```

### WordPress (80)
```bash
❯ nmap -sS -sV --script=http-wordpress-enum <IP>
# Enumeración general de WordPress

❯ nmap -sS -sV --script=http-wordpress-enum   --script-args type="themes" <IP>
# Temas instalados

❯ nmap -sS -sV --script=http-wordpress-enum   --script-args type="plugins" <IP>
# Plugins instalados

❯ nmap -sS -sV -p80,443 --script=http-wordpress-users <IP>
# Usuarios de WordPress
```

### FTP (21)
```bash
❯ nmap --script ftp-anon -p21 <IP>
# Verificar anonymous login

❯ nmap --script ftp-syst -p21 <IP>
# OS del servidor FTP

❯ nmap --script ftp-bounce -p21 <IP>
# Vulnerabilidad FTP bounce

❯ nmap <IP> --script ftp-brute   --script-args userdb=/root/users.txt -p21
# Fuerza bruta FTP
```

### SSH (22)
```bash
❯ nmap --script ssh2-enum-algos -p22 <IP>
# Algoritmos de cifrado SSH soportados

❯ nmap --script ssh-hostkey -p22 <IP>   --script-args ssh_hostkey=full
# SSH RSA host key completa

❯ nmap --script ssh-auth-methods -p22 <IP>   --script-args="ssh.user=<username>"
# Métodos de autenticación → si devuelve "none" → entrar sin creds

❯ nmap <IP> --script ssh-brute   --script-args userdb=/root/users.txt -p22
# Fuerza bruta SSH
```

### SNMP (161 UDP)
```bash
❯ nmap -sU -p161 --script=snmp-processes <IP>
# Procesos en ejecución

❯ nmap -sU -p161 --script=snmp-interfaces <IP>
# Interfaces de red

❯ nmap -sU -p161 --script=snmp-sysdescr <IP>
# Descripción del sistema → OS, versión

❯ nmap -sU -p161 --script=snmp-win32-users <IP>
# Usuarios Windows vía SNMP

❯ nmap -sU --script snmp-brute <IP>   --script-args snmp-brute.communitiesdb=/usr/share/wordlists/rockyou.txt
# Fuerza bruta de community strings
```

### NetBIOS (137 UDP / 139)
```bash
❯ nmap -sV -v --script nbstat.nse <IP>
# Enumeración NetBIOS

❯ nmap -sU -p137 --script nbstat.nse <IP>
# NetBIOS vía UDP 137
```

### MySQL (3306)
```bash
❯ nmap <IP> -p3306 --script=mysql-empty-password
# Usuarios sin contraseña

❯ nmap <IP> -p3306 --script=mysql-info
# Información y capabilities

❯ nmap <IP> -p3306 --script=mysql-users   --script-args="mysqluser='root',mysqlpass=''"
# Usuarios de la DB

❯ nmap <IP> -p3306 --script=mysql-databases   --script-args="mysqluser='root',mysqlpass=''"
# Bases de datos disponibles

❯ nmap <IP> -p3306 --script=mysql-variables   --script-args="mysqluser='root',mysqlpass=''"
# Variables del servidor → datadir y configs

❯ nmap <IP> -p3306 --script=mysql-audit   --script-args="mysql-audit.username='root',mysql-audit.password='',mysql-audit.filename='/usr/share/nmap/nselib/data/mysql-cis.audit'"
# Auditoría de privilegios

❯ nmap <IP> -p3306 --script=mysql-dump-hashes   --script-args="username='root',password=''"
# Dump de hashes de usuarios

❯ nmap <IP> -p3306 --script=mysql-query   --script-args="query='select count(*) from <db.tabla>;',username='root',password=''"
# Ejecutar query SQL
```

### MSSQL (1433)
```bash
❯ nmap <IP> -p1433 --script ms-sql-info
# Información del servidor

❯ nmap <IP> -p1433 --script ms-sql-ntlm-info   --script-args mssql.instance-port=1433
# Información adicional vía NTLM

❯ nmap <IP> -p1433 --script ms-sql-brute   --script-args userdb=/root/users.txt,passdb=/root/passwords.txt
# Fuerza bruta MSSQL

❯ nmap <IP> -p1433 --script ms-sql-empty-password
# Usuarios sin contraseña (sa user)

❯ nmap <IP> -p1433 --script ms-sql-query   --script-args mssql.username=admin,mssql.password=<pass>,ms-sql-query.query="SELECT * FROM <db.tabla>" -oN out.txt
# Ejecutar query SQL

❯ nmap <IP> -p1433 --script ms-sql-dump-hashes   --script-args mssql.username=admin,mssql.password=<pass>
# Dump de hashes

❯ nmap <IP> -p1433 --script ms-sql-xp-cmdshell   --script-args mssql.username=admin,mssql.password=<pass>,ms-sql-xp-cmdshell.cmd="whoami"
# RCE vía xp_cmdshell → cambiar el comando según necesidad
```

### LDAP (389)
```bash
❯ nmap --script ldap* -p389 <IP>
# Todos los scripts LDAP

❯ nmap --script ldap-rootdse -p389 <IP>
# Información base del servidor LDAP → naming contexts

❯ nmap --script ldap-search -p389 <IP>   --script-args ldap.base='"dc=domain,dc=com"'
# Búsqueda LDAP con base específica
```

### NFS (111 / 2049)
```bash
❯ nmap -sV --script=nfs-showmount <IP>
# Shares NFS disponibles

❯ nmap -p111 --script=nfs-ls,nfs-statfs,nfs-showmount <IP>
# Enumeración completa → listar, estadísticas y monturas
```

### RPC (111)
```bash
❯ nmap -p111 --script=nfs-ls,nfs-statfs,nfs-showmount <IP>
# Enumerar monturas RPC/NFS

❯ nmap -p111 --script=rpcinfo <IP>
# Listar todos los servicios RPC registrados
```

### Shellshock (Apache CGI)
```bash
❯ nmap -p80 <IP> --script=http-shellshock   --script-args "http-shellshock.uri=/gettime.cgi"
# Verificar Shellshock en CGI específico

❯ nmap --script http-shellshock   --script-args uri=/cgi-bin/user.sh -p80 <IP>
# Shellshock en cgi-bin
```

---

## 7. OUTPUT Y FORMATOS

```bash
❯ nmap -p- <IP> -oN output.txt
# Formato legible por humanos

❯ nmap -p- <IP> -oG output.gnmap
# Formato grepeable → para parsear con scripts

❯ nmap -p- <IP> -oX output.xml
# Formato XML → importar en Metasploit

❯ nmap -p- <IP> -oA output
# Los 3 formatos a la vez → usar siempre

❯ xsltproc output.xml > output.html
❯ python3 -m http.server 80
# Convertir XML a HTML y visualizar en navegador
```

---

## 8. REFERENCIA DE FLAGS

```bash
# Descubrimiento de hosts
-sn          # Ping scan → sin escaneo de puertos
-Pn          # Sin ping → asumir host activo
-n           # Sin resolución DNS

# Tipos de escaneo TCP
-sS          # SYN scan → rápido, sigiloso (root)
-sT          # TCP connect → sin root, más detectable
-sA          # ACK scan → mapear reglas de firewall
-sN          # NULL scan → evadir firewalls
-sF          # FIN scan → evadir firewalls/honeypots
-sX          # XMAS scan → evadir firewalls/IDS
-sW          # Window scan → estudiar firewall Windows

# UDP
-sU          # UDP scan

# Detección
-sV          # Versión de servicios
-sC          # Scripts por defecto
-O           # Sistema operativo
-A           # Agresivo: -sV -sC -O + traceroute

# Puertos
-p-          # Todos los puertos (65535)
-p 22,80,443 # Puertos específicos
-p 1-1000    # Rango
-F           # Top 100 más comunes
--top-ports N # N puertos más comunes
--open        # Solo puertos abiertos

# Velocidad y timing
-T0 / T1     # Paranoid / Sneaky → evasión máxima
-T2 / T3     # Polite / Normal → default
-T4 / T5     # Aggressive / Insane → laboratorios
--min-rate N  # Mínimo de paquetes por segundo

# Output
-v / -vv / -vvv   # Verbosidad incremental
-oN <file>        # Formato normal
-oG <file>        # Formato grepeable
-oX <file>        # Formato XML
-oA <file>        # Los 3 formatos

# Evasión
-D RND:N          # N decoys → IPs señuelo
-f                # Fragmentar paquetes
-g / --source-port # Puerto de origen
--spoof-mac 0     # Falsificar MAC aleatoria
--randomize-hosts  # Aleatorizar orden
--disable-arp-ping # Sin ARP ping
```

---

## ONE-LINERS MENTALES
- Siempre dos fases: RustScan/nmap rápido → nmap -sCV en puertos encontrados
- Windows → siempre -Pn → bloquea ICMP
- Guardar siempre con -oA → nunca perder resultados
- Laboratorio → --min-rate 5000 | Entorno real → --min-rate 500
- UDP clave → 53 (DNS), 69 (TFTP), 161 (SNMP)
- SMB → script smb-vuln-* primero → EternalBlue y otros CVEs
- SSH sin creds → ssh-auth-methods → si devuelve "none" → entrar directo
- Scripts disponibles → locate .nse | grep <servicio>
