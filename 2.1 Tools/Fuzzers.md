# Fuzzers Web 

Tags: #Fuzzing #Ffuf #Gobuster #Wfuzz #Feroxbuster #Dirb #Dirsearch #Dirbuster #Subdominios #Directorios #Vhost #Parametros #Extensions

## OBJETIVO
- Descubrir directorios y archivos ocultos en aplicaciones web
- Enumerar subdominios y virtual hosts
- Encontrar usuarios válidos y contraseñas en formularios web
- Identificar parámetros, extensiones y endpoints ocultos

## TIPS
1. **ffuf es el más rápido y flexible → úsalo como primera opción**
2. **Siempre filtrar por tamaño (-fs) o código (-fc) → reduce falsos positivos**
3. **Windows → asp, aspx, html, txt | Linux → php, php5, html, txt**
4. **Empezar con common.txt → confirmar que funciona → luego usar medium**
5. **301 sin contenido → agregar / al final de FUZZ o usar -r para follow redirect**
6. **Gobuster → más estable en conexiones lentas por manejo de sockets**
7. **Hacer una petición base primero → anotar tamaño/código → usarlo como filtro**
8. **Si encuentras un directorio → fuzzearlo también con recursión o manualmente**

---

## WORDLISTS DE REFERENCIA RÁPIDA

```bash
# Directorios y archivos — General
/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt     # Más completa → usar siempre
/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt      # Más rápida
/usr/share/dirb/wordlists/common.txt                             # Prueba inicial rápida
/usr/share/seclists/Discovery/Web-Content/big.txt                # Alternativa grande
/usr/share/metasploit-framework/data/wordlists/directory.txt     # Apps empresariales

# Directorios y archivos — Raft (muy buenas para archivos)
/usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt
/usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt
/usr/share/seclists/Discovery/Web-Content/raft-large-files.txt
/usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt

# Subdominios y VHosts
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt    # Rápida
/usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt   # Más completa
/usr/share/seclists/Discovery/DNS/namelist.txt                       # Alternativa

# Parámetros GET/POST
/usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt
/usr/share/seclists/Discovery/Web-Content/api/api-endpoints.txt

# CMS específicos
/usr/share/seclists/Discovery/Web-Content/CMS/wordpress.fuzz.txt
/usr/share/seclists/Discovery/Web-Content/CMS/joomla.txt
/usr/share/seclists/Discovery/Web-Content/CMS/drupal.txt

# Usuarios
/usr/share/seclists/Usernames/Names/names.txt
/usr/share/seclists/Usernames/xato-net-10-million-usernames.txt

# Contraseñas
/usr/share/wordlists/rockyou.txt
/usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-100.txt
/usr/share/seclists/Passwords/Common-Credentials/best110.txt
```


---

## FILTROS DE REFERENCIA RÁPIDA

| Herramienta | Filtrar tamaño        | Filtrar código          | Filtrar palabras    | Follow redirect |
| ----------- | --------------------- | ----------------------- | ------------------- | --------------- |
| ffuf        | -fs \<size>           | -fc \<code>             | -fw \<words>        | -r              |
| gobuster    | —                     | -b \<code>              | —                   | -r              |
| wfuzz       | --hh \<chars>         | --hc \<code>            | --hw \<words>       | -L              |
| feroxbuster | --filter-size \<size> | --filter-status \<code> | --filter-words \<n> | -r              |
| dirsearch   | --exclude-sizes       | --exclude-status        | —                   | —               |

---

## EXTENSIONES POR OS

```bash
# Linux
.php .php5 .php7 .html .htm .txt .xml .json .bak .old .conf .log .sh

# Windows
.asp .aspx .html .htm .txt .xml .config .bak .old .log .ps1

# APIs / Genéricas
.json .xml .yaml .yml .api .graphql
```

---

## 1. ENUMERACIÓN DE DIRECTORIOS Y ARCHIVOS

### feroxbuster
```bash
❯ feroxbuster -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -d 2
# -d 2 → profundidad de recursión | Mejor para recursión que gobuster

❯ feroxbuster -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt   -d 2 -x php,html,txt -t 100
# -x → extensiones | -t → hilos

❯ feroxbuster -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt   -d 2 --filter-status 404,403 --filter-size 0
# --filter-status → equivalente a -b en gobuster
# --filter-size → filtrar respuestas vacías
```

### ffuf
```bash
❯ ffuf -u http://<IP>/FUZZ -w /usr/share/dirb/wordlists/common.txt
# Prueba inicial → confirmar que el fuzzing funciona antes de wordlists grandes

❯ ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
# Enumeración completa de directorios

❯ ffuf -u http://<IP>/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt
# Enfocado en archivos → mejor que directory-list para encontrar archivos sueltos

❯ ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e .php,.txt,.html
# Buscar extensiones específicas junto con la wordlist
# Linux: .php,.php5,.html,.txt | Windows: .asp,.aspx,.html,.txt,.config

❯ ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -recursion -recursion-depth 2
# Enumeración recursiva → entra en los directorios encontrados automáticamente
# -recursion-depth 2 → evita loops infinitos

❯ ffuf -c -t 200 -u https://<IP>/FUZZ/ -v   -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt --mc=200
# -c → colores | -t 200 → 200 hilos | -v → verbose con redirecciones
# --mc=200 → solo mostrar 200 | / al final → follow redirect 301

❯ ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -fs 0 -fc 404,403
# -fs 0 → filtrar respuestas vacías | -fc → filtrar códigos
```

### gobuster
```bash
❯ gobuster dir -u http://<IP>/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 50 -b 403,404
# -b → blacklist de códigos a ocultar

❯ gobuster dir -u http://<IP>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -b 403,404 -x .php,.html,.txt,.xml -r
# -x → extensiones | -r → follow redirect

❯ gobuster dir -u http://<IP>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x asp,aspx,html,txt -f
# -f / --add-slash → agrega / al final → código real en vez de 301
# Windows: asp,aspx,html,txt | Linux: php,html,txt,php5

❯ gobuster dir -u https://<IP>/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 200 -s 200 -x html -b " "
# -s 200 → solo mostrar 200 | -b " " → evitar error de blacklist en HTTPS
```

### wfuzz
```bash
❯ wfuzz -c --hc=404,403 -t 200 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt https://<IP>/FUZZ

❯ wfuzz -c -L --hc=404,403 -t 200 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt https://<IP>/FUZZ
# -L → follow redirect 301 | Si no muestra nada → quitar -L y agregar / al final

❯ wfuzz -c --hc=404,403 -t 200 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt   https://<IP>/FUZZ.html
# Buscar extensión específica

❯ wfuzz -c --hc=404,403 -t 200 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -z list,html-txt-php https://<IP>/FUZZ.FUZ2Z
# -z list → payload de extensiones | FUZ2Z → segunda posición de fuzzing
```

### Filtros de wfuzz — referencia
```bash
--hc=404,403   # HideCode → ocultar por código de estado
--hl=216       # HideLine → ocultar por número de líneas
--hw=6515      # HideWords → ocultar por número de palabras
--hh=12345     # HideCharacters → ocultar por número de caracteres
--sc=200       # ShowCode → mostrar solo ese código
--sl=216       # ShowLine → mostrar solo ese número de líneas
--sw=6515      # ShowWords → mostrar solo ese número de palabras
--sh=12345     # ShowCharacters → mostrar solo ese número de caracteres
```

### dirb
```bash
❯ dirb http://<IP>/
# Wordlist interna → prueba inicial muy rápida

❯ dirb http://<IP>/  /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -X .php
# -X → extensión específica

❯ dirb http://<IP> /usr/share/metasploit-framework/data/wordlists/directory.txt
# Wordlist de Metasploit → buena para apps empresariales

❯ dirb https://<IP>/
# Puerto 443 → HTTPS
```

### dirsearch
```bash
❯ dirsearch -u http://<IP>/ -t 30 -e txt,html,php -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
# -e → extensiones | -t → hilos

❯ dirsearch -u http://<IP>/ -t 30 -e txt,html,php,jsp,asp,aspx,rar,zip -f -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
# -f → forzar extensiones en cada palabra → más completo pero más lento

❯ dirsearch -u http://<IP>/ -t 30 -e php,html,txt --exclude-status 403,404
# --exclude-status → equivalente a -b en gobuster
```

### dirbuster (gráfico)
```bash
❯ dirbuster &
# URL → http://IP/ (seguir sintaxis exacta con http://)
# Go Faster → activar para escaneo más rápido
# File extension → pdf,docx,rar,php,zip,txt (más extensiones = más lento)
# Browse → /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

---

## 2. ENUMERACIÓN DE SUBDOMINIOS Y VHOSTS

### ffuf
```bash
# PASO 1 → Hacer petición base para obtener tamaño a filtrar
❯ curl -s http://<IP>/ -H "Host: nonexistent.domain.com" | wc -c
# Anotar el número → usarlo en -fs

❯ ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.<domain.com>" -u http://<IP>/ -fs <tamaño_base>
# -fs → filtrar tamaño base → ajustar al número obtenido arriba

❯ ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.<domain.com>" -u http://<IP>/ -fs <tamaño_base> -mc all
# -mc all → ver todos los códigos → útil para calibrar filtros

❯ ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: preprod-FUZZ.<domain.com>" -u http://<IP>/ -fs <tamaño_base>
# Probar prefijos comunes: preprod-, dev-, staging-, test-, admin-, api-
```

### gobuster
```bash
❯ gobuster vhost --append-domain   -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --url https://<domain.com>/ -t 200 -k
# --append-domain → añade dominio base | -k → ignorar errores SSL

❯ gobuster vhost --append-domain   -u https://<domain.com>/ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t 200 | grep -v "403"
```

### wfuzz
```bash
❯ wfuzz -c --hc=404 --hh=<tamaño_base> -t 200 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.<domain.com>" https://<domain.com>
# --hh → ajustar al tamaño de respuesta base

❯ wfuzz -c --hc=404 --hh=<tamaño_base> -t 200 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: preprod-FUZZ.<domain.com>" https://<domain.com>
```

### Insight
- Subdominio encontrado → agregar al /etc/hosts → escanear puertos completos
- Si hay muchos resultados → el filtro está mal calibrado → ajustar -fs o --hh

---

## 3. FUZZING DE PARÁMETROS GET

```bash
❯ ffuf -u "http://<IP>/page?FUZZ=value" -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fs <tamaño_base>
# Descubrir nombres de parámetros GET ocultos

❯ ffuf -u "http://<IP>/page?id=FUZZ"   -w /usr/share/seclists/Fuzzing/Integers/Integers.txt -fs <tamaño_base>
# Fuzzear valores de parámetros numéricos → IDOR

❯ wfuzz -c --hw=<palabras_base> -t 200 -z range,1-20000 "https://<IP>/shop/detail?product_id=FUZZ"
# Rango numérico → útil para IDOR y enumeración de objetos
```

---

## 4. FUZZING DE PARÁMETROS POST

```bash
❯ ffuf -u http://<IP>/api/endpoint -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -X POST -d "FUZZ=test"   -H "Content-Type: application/x-www-form-urlencoded" -fs <tamaño_base>
# Descubrir parámetros POST ocultos en endpoints

❯ ffuf -u http://<IP>/api/endpoint -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -X POST -d "FUZZ=test"   -H "Content-Type: application/json" -fs <tamaño_base>
# Mismo pero con JSON → cambiar Content-Type
```

---

## 5. ENUMERACIÓN DE USUARIOS EN FORMULARIOS WEB

```bash
❯ ffuf -w /usr/share/seclists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://<IP>/customers/signup -mr "username already exists"
# -mr → match response → texto que confirma usuario válido
# Ajustar -d a los campos reales del formulario
# Ajustar -mr al mensaje de error/éxito de la app

❯ ffuf -w /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt -X POST -d "username=FUZZ&password=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://<IP>/login -mr "Invalid password" -mc all
# -mr "Invalid password" → si el error es diferente entre usuario válido e inválido
# -mc all → ver todos los códigos para calibrar
```

---

## 6. FUERZA BRUTA DE CONTRASEÑAS EN FORMULARIOS WEB

```bash
❯ ffuf   -w valid_usernames.txt:W1,/usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://<IP>/customers/login -fc 200
# W1 → usuarios válidos | W2 → wordlist de passwords
# -fc 200 → filtrar 200 (login fallido) → mostrar lo diferente (302 = login ok)
# Ajustar -fc al código de respuesta del login fallido de la app
```

---

## 7. FUZZING DE EXTENSIONES

```bash
❯ ffuf -u http://<IP>/indexFUZZ -w /usr/share/seclists/Discovery/Web-Content/web-extensions.txt -fs <tamaño_base>
# Descubrir extensión de un archivo conocido

❯ gobuster dir -u http://<IP>/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt -x php,php5,php7,html,htm,asp,aspx,txt,xml,json,bak,old,conf,config,ini,log -t 50 -b 403,404
# Buscar múltiples extensiones en todos los archivos
```

---

## 8. FUZZING EN CMS

### WordPress
```bash
❯ wfuzz -c --hc=404 -t 200 -w /usr/share/seclists/Discovery/Web-Content/CMS/wordpress.fuzz.txt   http://<IP>/FUZZ
# Descubrir plugins y temas de WordPress

❯ gobuster dir -u http://<IP>/wp-content/plugins/ -w /usr/share/seclists/Discovery/Web-Content/CMS/wordpress.fuzz.txt -t 50 -b 403,404
# Enumeración directa de plugins
```

### Joomla
```bash
❯ ffuf -u http://<IP>/FUZZ -w /usr/share/seclists/Discovery/Web-Content/CMS/joomla.txt -fc 404,403
```

### Drupal
```bash
❯ ffuf -u http://<IP>/FUZZ -w /usr/share/seclists/Discovery/Web-Content/CMS/drupal.txt -fc 404,403
```

---

## ONE-LINERS MENTALES
- Primera enumeración → ffuf con common.txt → confirmar antes de wordlist grande
- Recursión → feroxbuster -d 2 → más completo en una sola ejecución
- Subdominios → curl base primero para tamaño → ffuf con Host header + -fs
- Login usuarios → ffuf -mr con texto que confirma usuario válido
- Login passwords → ffuf W1:W2 con -fc del código de login fallido
- Windows → .asp,.aspx,.config | Linux → .php,.php5
- 301 vacío → / al final de FUZZ o -r
- Muchos falsos positivos → calibrar filtro con petición base primero
