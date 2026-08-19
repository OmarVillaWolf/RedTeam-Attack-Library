# Linux — Referencia de Comandos

Tags: #Linux #Comandos #Referencia #Kali #FileTransfer #Red #Archivos

## OBJETIVO

Referencia rápida de comandos Linux organizados por contexto de uso. No es una metodología — es un índice de comandos útiles en diferentes situaciones.

---

## 1. CONFIGURACIÓN DEL ENTORNO ATACANTE (KALI)

### Activar SSH en Kali

```bash
❯ sudo update-rc.d -f ssh remove
❯ sudo update-rc.d ssh defaults
❯ sudo update-rc.d ssh enable
❯ sudo reboot

❯ sudo systemctl status ssh       # Verificar que SSH está corriendo
❯ sudo systemctl start ssh        # Iniciar SSH sin reiniciar
```

### Activar RDP en Kali

```bash
❯ sudo apt install xrdp -y
❯ sudo adduser xrdp ssl-cert      # Agregar xrdp al grupo ssl-cert
❯ sudo systemctl enable xrdp
❯ sudo reboot
```

### Servicios comunes

```bash
❯ service apache2 start           # Iniciar Apache → servidor de archivos
❯ service apache2 stop
❯ sudo systemctl status apache2

❯ service mysql start             # Iniciar MySQL local
❯ service postgresql start        # Iniciar PostgreSQL (Metasploit)
```

### Actualización del sistema

```bash
❯ apt update && apt upgrade       # Kali/Debian
❯ apt full-upgrade                # Actualización completa incluyendo dependencias
❯ parrot-upgrade                  # Parrot OS
```

### Instalación de herramientas

```bash
❯ pip3 install pwn --break-system-packages    # pwntools
❯ sudo apt install arp-scan                   # ARP-Scan
❯ sudo updatedb                               # Actualizar base de datos de locate
```

## 2. RECONOCIMIENTO DE RED LOCAL

```bash
❯ ping -c 1 <IP>
# Verificar si el host está activo
# TTL=64 → Linux | TTL=128 → Windows

❯ ping -c 1 <IP> -R
# -R → traceroute → solo funciona en Linux

❯ fping -I ens33 -g <IP/24> -a 2>/dev/null
# Ping a múltiples IPs de una subred → -a → mostrar solo activos

❯ netdiscover -i ens33 -r <IP/24>
# Descubrir hosts via ARP

❯ arp-scan -I ens33 --localnet --ignoredups
# Escaneo ARP de la red local → muestra MAC address
# --ignoredups → ignorar IPs duplicadas

❯ masscan -p21,22,80,139,443,445,8080 -Pn <IP/16> --rate=5000
# Escaneo masivo de puertos → más rápido que nmap
# Útil para redes grandes con /16 o /8

❯ ip a                            # Ver interfaces e IPs
❯ ifconfig                        # Alternativa a ip a
❯ ifconfig docker0                # Ver IP de interfaz Docker
❯ hostname -I                     # Solo mostrar IPs

❯ route -n                        # Tabla de ruteo
❯ ip route                        # Alternativa moderna a route

❯ netstat -nat                    # Conexiones y puertos abiertos
❯ netstat -tupln                  # Puertos en escucha con PID
❯ ss -tlnp                        # Alternativa moderna a netstat (más rápida)
```

- [Calculadora CIDR](https://www.ipaddressguide.com/cidr)

---

## 3. ANÁLISIS DE ARCHIVOS

### Identificar tipo y contenido

```bash
❯ file <archivo>
# Identificar tipo de archivo por magic bytes → no confiar en la extensión

❯ strings <archivo.exe>
# Extraer cadenas de texto de un binario → buscar credenciales, rutas, keys

❯ exiftool <imagen.jpg>
# Ver metadatos de imágenes y PDFs → puede revelar usuarios, rutas, versiones

❯ md5sum <archivo>
# Obtener hash MD5 del archivo → verificar integridad

❯ sha256sum <archivo>
# Hash SHA256 → más seguro que MD5

❯ du -hc <archivo>
# Ver tamaño del archivo
```

### Esteganografía

```bash
❯ steghide info <imagen.jpg>
# Ver si la imagen tiene contenido oculto

❯ steghide extract -sf <imagen.jpg>
# Extraer contenido oculto → pedirá contraseña si está protegido

❯ binwalk <archivo>
# Buscar archivos embebidos dentro de otro archivo

❯ binwalk -e <archivo>
# Extraer archivos embebidos automáticamente
```

### Análisis de binarios

```bash
❯ radare2 <archivo.exe>
  ❯ aaa               # Analizar todas las funciones
  ❯ afl               # Listar funciones encontradas
  ❯ s main            # Ir a la función main
  ❯ pdf               # Disassembly de la función actual

❯ ltrace ./<binario>  # Trazar llamadas a librerías
❯ strace ./<binario>  # Trazar llamadas al sistema
```

### Archivos XLSX

```bash 
❯ unzip -p file.xlsx xl/workbook.xml     # Ver datos del archivo 

# Mirar el contenido del archivo 
❯ mkdir accounts_unzip   
❯ unzip -q accounts.xlsx -d accounts_unzip
❯ cat accounts_unzip/xl/sharedStrings.xml

# Mirar el contenido en una columna 
❯ unzip -p accounts.xlsx xl/sharedStrings.xml | sed 's/<[^>]*>/\n/g' | sed '/^$/d'
```

### Compresión y descompresión

```bash
❯ 7z l <archivo.zip>          # Ver contenido sin extraer (l=ele)
❯ 7z x <archivo.gz>           # Extraer contenido
❯ tar -xf <archivo.tar.gz>    # Descomprimir tar.gz
❯ tar -xvf <archivo.tar.gz>   # Con verbose
❯ tar -czf output.tar.gz dir/ # Comprimir directorio
❯ gzip -d <archivo.txt.gz>    # Descomprimir gz
❯ gunzip <archivo.gz>         # Alternativa a gzip -d
❯ unzip <archivo.zip>         # Descomprimir zip
❯ unzip -l <archivo.zip>      # Ver contenido sin extraer
```

---

## 4. TRANSFERENCIA DE ARCHIVOS

### Descargar desde el objetivo

```bash
❯ wget http://<IP>/<archivo>
# Descargar archivo desde servidor HTTP

❯ curl http://<IP>:<PORT>/<archivo> -o <nombre_local>
# -o → guardar con nombre específico

❯ curl http://<IP>:<PORT> -v
# -v → ver cabeceras de la respuesta
```

### Servir archivos desde Kali

```bash
❯ python3 -m http.server 80
# Servidor HTTP simple → servir archivos del directorio actual

❯ python3 -m http.server 8080
# Puerto alternativo si 80 requiere root

❯ php -S 0.0.0.0:80
# Servidor HTTP con PHP → alternativa

❯ service apache2 start
# Apache → para servir archivos de /var/www/html/
```

### Base64 para transferir archivos

```bash
# En la máquina origen → codificar
❯ base64 -w 0 archivo.sh
# -w 0 → sin saltos de línea → más fácil de copiar

❯ base64 data.zip
# Codificar zip para transferir por consola

# En la máquina destino → decodificar
❯ echo <base64_string> | base64 -d > archivo.sh
# Reconstruir el archivo original

❯ cat data.zip | base64 -d | sponge data.zip
# Decodificar y reemplazar el archivo original con sponge

❯ echo -n 'texto' | base64
# Codificar texto simple en base64
```

### Netcat para transferir archivos

```bash
# Receptor (ejecutar primero)
❯ nc -lvnp <PORT> > archivo_recibido.txt

# Emisor
❯ nc <IP_receptor> <PORT> < archivo_a_enviar.txt

# Recibir shell reversa
❯ nc -nlvp 443
```

### Reducir tamaño antes de transferir

```bash
❯ go build -ldflags "-s -w" <archivo.go>
# Compilar Go sin debug info → más pequeño

❯ upx <archivo>
# Comprimir ejecutable → menor tamaño para transferir
```

---

## 5. BÚSQUEDA Y MANIPULACIÓN DE ARCHIVOS

### Buscar archivos

```bash
❯ find /var/www -type f -exec grep -i "password" {} + 2>/dev/null 
# Método eficiente 

❯ find /var/www -type f \( -name "*.php" -o -name "*.env" -o -name "*.conf" \)
# Buscar archivos interesantes 

❯ grep -RiE "password|passwd|pwd|db_pass|db_password|secret|key|token" /var/www 2>/dev/null

❯ grep -Ri "password" /var/www/ 2>/dev/null    # → configs web (ORO puro)
❯ grep -Ri "password" /etc/ 2>/dev/null        # → configs del sistema
❯ grep -Ri "password" /home/ 2>/dev/null       # → creds hardcodeadas por usuarios

❯ grep -Ri "password" /var/www --include="*.php" 2>/dev/null
❯ grep -Ri "password" / --include="*.conf" 2>/dev/null
❯ grep -Ri "password" / --include="*.env" 2>/dev/null
❯ grep -Ri "password" / --include="*.ini" 2>/dev/null
# Extenciones interesantes 

❯ find / -name <archivo.txt> 2>/dev/null
# Buscar desde la raíz → 2>/dev/null → silenciar errores de permisos

❯ find . -name "*conf*"
# Buscar archivos con "conf" en el nombre → directorio actual

❯ find / -perm -4000 2>/dev/null
# Buscar archivos SUID

❯ find / -writable 2>/dev/null
# Archivos escribibles por el usuario actual

❯ find / -name "*.php" 2>/dev/null
# Buscar por extensión

❯ locate <archivo>
# Buscar en la base de datos del sistema → más rápido que find
# Actualizar primero: sudo updatedb

❯ locate nc.exe
# Buscar Netcat para Windows → para transferir a la víctima
```

### Leer y manipular archivos

```bash
❯ cat <archivo>               # Ver contenido completo
❯ head <archivo>              # Primeras 10 líneas
❯ head -n 20 <archivo>        # Primeras N líneas
❯ tail <archivo>              # Últimas 10 líneas
❯ tail -f <archivo>           # Ver en tiempo real → logs
❯ less <archivo>              # Paginador → navegable
❯ nano <archivo>              # Editor de texto básico
❯ grep "patrón" <archivo>     # Buscar patrón en archivo
❯ grep -R "patrón" .          # Buscar recursivamente en directorio
❯ grep -ri "password" .       # Case insensitive + recursivo
❯ cat <archivo> | wc -l       # Contar líneas
❯ echo -n "texto" | wc -c     # Contar caracteres

❯ echo 'contenido' > archivo  # Crear/sobreescribir archivo
❯ echo 'contenido' >> archivo # Añadir al final del archivo
```

### Copiar y clipboard

```bash
❯ cat <archivo> | xclip -sel clip
# Copiar output al portapapeles

❯ cp /bin/bash .
# Copiar bash al directorio actual → útil en PrivEsc con SUID

❯ !$
# Referenciar el último argumento del comando anterior
```

---

## 6. PERMISOS Y PROPIETARIOS

```bash
❯ ls -la                          # Ver archivos con permisos y propietario

❯ chmod u+x <archivo>             # Dar permiso de ejecución al usuario
❯ chmod 4755 bash                 # Asignar SUID → aparece 's' en permisos
❯ chmod 600 id_rsa                # Permisos correctos para clave SSH privada
❯ chmod 777 <archivo>             # Todos los permisos a todos → no recomendable

❯ chown root:root bash            # Cambiar propietario y grupo a root
❯ chown <user>:<group> -R <dir>   # Cambiar propietario recursivamente

# Monitorear cuando un archivo cambia a SUID
❯ watch -n 1 ls -l /bin/bash
# Ejecutar ls cada 1 segundo → ver cuando cambia
```

---

## 7. PROCESOS Y SERVICIOS

```bash
❯ ps aux                          # Ver todos los procesos
❯ ps aux | grep <proceso>         # Filtrar por proceso específico
❯ top / htop                      # Monitor de procesos en tiempo real

❯ kill -9 <PID>                   # Matar proceso por PID
❯ kill -9 $(lsof -i:80 -t)       # Matar proceso que usa el puerto 80

❯ lsof -i:80                      # Ver qué proceso usa el puerto 80
❯ pwdx <PID>                      # Ver ruta de ejecución de un proceso

# Monitorear procesos en tiempo real (útil para ver cron)
❯ watch -n 1 "ps aux | grep <proceso>"
```

---

## 8. ESCUCHA EN RED

```bash
❯ nc -nlvp 443                    # Netcat en escucha → recibir reverse shell
❯ nc -nlvp 4444                   # Puerto alternativo

❯ tcpdump -i tun0 icmp -n
# Escuchar trazas ICMP en tun0 → verificar RCE con ping
# -n → sin resolución DNS

❯ tcpdump -i tun0 -w captura.pcap
# Capturar todo el tráfico de la interfaz a archivo

❯ rlwrap nc -nlvp 443
# rlwrap → añade historial y flechas a netcat → más cómodo para shells
```

---

## 9. ENCODING Y CONVERSIÓN

```bash
❯ echo -n 'texto' | base64        # Codificar en base64
❯ echo 'base64string' | base64 -d # Decodificar base64

❯ echo '50 40 73 73 77...' | xargs | xxd -ps -r
# Convertir hex a texto
# xargs → una sola línea | xxd -ps -r → hex a binario

❯ python3 -c "print(bytes.fromhex('50407373'))"
# Convertir hex a texto con Python

❯ echo -n 'texto' | xxd
# Ver representación hexadecimal de texto
```

---

## 10. GIT Y REPOSITORIOS

```bash
❯ git clone https://<URL>
# Clonar repositorio completo

❯ svn checkout https://<URL>/trunk/<subcarpeta>
# Clonar solo una subcarpeta de GitHub
# Cambiar /tree/master por /trunk en la URL

❯ git log --oneline
# Ver historial de commits → buscar credenciales en commits anteriores

❯ git show <commit_hash>
# Ver cambios de un commit específico

❯ git diff HEAD~1
# Ver diferencias con el commit anterior
```

---

## 11. RUTAS Y DIRECTORIOS DE REFERENCIA

### Directorios con escritura en Linux

```bash
/tmp                    # Lectura y escritura → se limpia al reiniciar
/var/tmp               # Persiste entre reinicios
/dev/shm               # Memoria compartida → muy rápido
/temp                  # En contexto PHP
```

### Archivos críticos del sistema

```bash
/etc/hosts             # Resolución de nombres local
/etc/passwd            # Usuarios del sistema
/etc/shadow            # Hashes de contraseñas → solo root
/etc/group             # Grupos del sistema
/etc/shells            # Shells disponibles
/etc/ssh/sshd_config   # Configuración SSH → ver si root puede conectar
/etc/crontab           # Tareas cron del sistema
/etc/sudoers           # Permisos sudo
```

### Rutas útiles durante pentesting

```bash
/var/www/html/         # Raíz web Apache/Nginx
/var/www/html/config.php        # Credenciales DB frecuentes
/var/www/html/wp-config.php     # Credenciales WordPress
C:\inetpub\wwwroot\             # Raíz web IIS (Windows)
```

### Atajos útiles en terminal

```bash
Ctrl + U    # Borrar línea completa hacia la izquierda
Ctrl + K    # Borrar todo a la derecha del cursor
Ctrl + R    # Búsqueda en historial
!$          # Último argumento del comando anterior

❯ pushd /usr/share/    # Ir al directorio guardando el actual
❯ popd                 # Volver al directorio anterior
```

---

## 12. BORRADO SEGURO DE EVIDENCIA

```bash
❯ scrub -p dod <archivo>
# Sobreescribir con patrón DoD → primer paso

❯ shred -zun 10 -v <archivo>
# -z → añadir ceros al final | -u → borrar después | -n 10 → 10 pasadas
# A más pasadas → más difícil de recuperar

❯ shred -zun 10 -v archivo*
# Borrar todos los archivos que empiecen con el nombre

❯ echo ' ' > ~/.zsh_history
# Limpiar historial de zsh

❯ history -c && history -w
# Limpiar historial de bash en memoria y archivo
```