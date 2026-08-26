# Password Cracking

Tags: #Hashcat #JohnTheRipper #PasswordCracking #HashIdentifier #Fcrackzip #Crackpkcs12 #OpenSSL #BruteForce #Dictionary

## OBJETIVO
- Identificar el tipo de hash antes de intentar crackearlo
- Crackear hashes offline con diccionario, reglas o fuerza bruta
- Extraer hashes de archivos protegidos (ZIP, SSH, KeePass, PFX, etc.)

## TIPS
1. **Identificar el hash PRIMERO → usar el modo equivocado = tiempo perdido**
2. **Hashcat es más rápido con GPU → John es más versátil para formatos de archivos**
3. **Siempre probar rockyou.txt primero → si no crackea, agregar reglas**
4. **--show en John y hashcat → ver hashes ya crackeados en sesiones anteriores**
5. **Si hashcat falla → probar John con el mismo hash → a veces detecta el formato mejor**
6. **Las reglas multiplican las variantes de la wordlist → probar antes de fuerza bruta pura**

## TOOLS
* [Hashes.com — Identificar](https://hashes.com/en/tools/hash_identifier)
* [Hashes.com — Crackear online](https://hashes.com/en/decrypt/hash)
* [CrackStation](https://crackstation.net/)
* [Hashcat Example Hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)

---

## 0. IDENTIFICAR EL TIPO DE HASH

```bash
❯ hashid <hash>
# Identifica el tipo de hash → devuelve posibles formatos
# Ejemplo: hashid 2b22337f218b2d82dfc3b6f77e7cb8ec

❯ hash-identifier
# Herramienta interactiva → pegar el hash y muestra el tipo
# Alternativa cuando hashid no es concluyente

❯ echo -n 'hash' | wc -c
# Contar caracteres del hash → ayuda a identificar el tipo
# MD5 = 32 chars | SHA1 = 40 | SHA256 = 64 | SHA512 = 128

❯ hashcat --example-hashes
# Ver todos los tipos de hash soportados con ejemplos

❯ hashcat --example-hashes | grep -i "ntlm" -A 2
# Filtrar por tipo específico y ver las líneas siguientes
```

### Referencia de prefijos de hash
| Prefijo      | Algoritmo                     |
| ------------ | ----------------------------- |
| \$1$         | MD5                           |
| \$2$ o \$2a$ | Blowfish                      |
| \$2y$        | bcrypt                        |
| \$5$         | SHA-256                       |
| \$6$         | SHA-512                       |
| \$sha1$      | SHA1crypt                     |
| \$y$         | Yescrypt                      |
| \$P$ o \$H$  | phpass (WordPress/Joomla MD5) |
| \$krb5asrep$ | AS-REP Roasting               |
| \$krb5tgs$   | Kerberoasting                 |

### Referencia de longitud de hash
```
32  chars → MD5
40  chars → SHA1
56  chars → SHA224
60  chars → bcrypt
64  chars → SHA256
96  chars → SHA384
128 chars → SHA512
```

### Formato NTLM en secretsdump
```
# Solo se necesita la parte NT (después del tercer :)
administrator:500:LM:NT:::
admin:500:aad3b435b51404eeaad3b435b51404ee:71759a1bb2web4da43e676d6b7190711:::
#                                           ↑ Esta parte es el hash NT
```

---

## 1. HASHES DE CONTRASEÑAS COMUNES

### MD5
```bash
# hashcat
❯ hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt
# -m 0 → MD5

❯ hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule
❯ hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule
# Con reglas → más variantes

# john
❯ john --format=Raw-MD5 hash.txt -w=/usr/share/wordlists/rockyou.txt 
❯ john --show --format=Raw-MD5 hash.txt
# --show → ver resultados ya crackeados
```

### SHA1
```bash
# hashcat
❯ hashcat -m 100 hash.txt /usr/share/wordlists/rockyou.txt

# john
❯ john --format=Raw-SHA1 hash.txt -w=/usr/share/wordlists/rockyou.txt 
```

### SHA256
```bash
# hashcat
❯ hashcat -m 1400 hash.txt /usr/share/wordlists/rockyou.txt

# john
❯ john --format=Raw-SHA256 hash.txt -w=/usr/share/wordlists/rockyou.txt 
```

### SHA512
```bash
# hashcat
❯ hashcat -m 1700 hash.txt /usr/share/wordlists/rockyou.txt

# john
❯ john --format=Raw-SHA512 hash.txt -w=/usr/share/wordlists/rockyou.txt 
```

### bcrypt
```bash
# hashcat
❯ hashcat -m 3200 hash.txt /usr/share/wordlists/rockyou.txt -O
# -O → optimización | bcrypt es muy lento → ser paciente

# john
❯ john --format=bcrypt hash.txt -w=/usr/share/wordlists/rockyou.txt 
```

### phpass (WordPress / Joomla MD5 — empieza con $P$ o $H$)
```bash
# hashcat
❯ hashcat -m 400 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
# -m 400 → phpass WordPress/Joomla

# john
❯ john --format=phpass hash.txt -w=/usr/share/wordlists/rockyou.txt 
```

---

## 2. HASHES DE WINDOWS / AD

### NTLM (hash NT — de SAM, secretsdump, mimikatz)
```bash
# hashcat
❯ hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt
# -m 1000 → NTLM (hash NT puro)

❯ hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule

# john
❯ john --format=NT hash.txt -w=/usr/share/wordlists/rockyou.txt 
❯ john --show hash.txt
```

### NTLMv2 (NetNTLMv2 — capturado con Responder)
```bash
# hashcat
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --force -O
# -m 5600 → NetNTLMv2 | --force → ignorar warnings | -O → optimizar

❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule --force

# john
❯ john --format=netntlmv2 hash.txt -w=/usr/share/wordlists/rockyou.txt 

# NTLMv1 (menos común)
❯ hashcat -m 5500 hash.txt /usr/share/wordlists/rockyou.txt --force
```

### AS-REP Roasting (\$krb5asrep$)
```bash
# hashcat
❯ hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt
❯ hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt   -r /usr/share/hashcat/rules/best66.rule
❯ hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt   --rules /usr/share/hashcat/rules/InsidePro-PasswordsPro.rule --force
❯ hashcat -m 18200 hash_brandon /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule -r /usr/share/hashcat/rules/rockyou-30000.rule

# john
❯ john --format=krb5asrep hash.txt -w=/usr/share/wordlists/rockyou.txt 

# Ver resultados
❯ hashcat -m 18200 hash.txt --show
```

### Kerberoasting (\$krb5tgs$)
```bash
# hashcat
❯ hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt
# RC4 → más común y más rápido de crackear

❯ hashcat -m 19700 hash.txt /usr/share/wordlists/rockyou.txt
# AES256 → más lento

❯ hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt   -r /usr/share/hashcat/rules/best66.rule
❯ hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt   --rules /usr/share/hashcat/rules/InsidePro-PasswordsPro.rule --force

# john
❯ john --format=krb5tgs hash.txt -w=/usr/share/wordlists/rockyou.txt 

# Ver resultados
❯ hashcat -m 13100 hash.txt --show
```

---

## 3. HASHES DE BASES DE DATOS

### MSSQL
```bash
# hashcat
❯ hashcat -m 1731 -a 0 hash.txt /usr/share/wordlists/rockyou.txt --force
# -m 1731 → MSSQL (2012, 2014)
❯ hashcat -m 132 hash.txt /usr/share/wordlists/rockyou.txt
# -m 132 → MSSQL (2000)
❯ hashcat -m 131 hash.txt /usr/share/wordlists/rockyou.txt
# -m 131 → MSSQL (2005)

# john
❯ john --format=mssql12 hash.txt -w=/usr/share/wordlists/rockyou.txt 

# Ver resultado
❯ hashcat -m 1731 hash.txt --show
```

### MySQL
```bash
# hashcat
❯ hashcat -m 300 hash.txt /usr/share/wordlists/rockyou.txt
# -m 300 → MySQL4.1/MySQL5+

# john
❯ john --format=mysql-sha1 hash.txt -w=/usr/share/wordlists/rockyou.txt 
```

---

## 4. HASHES DE ARCHIVOS PROTEGIDOS

### ZIP
```bash
# Extraer hash primero
❯ zip2john file.zip > hash.txt
# Genera el hash del ZIP para crackearlo

# john
❯ john hash.txt -w=/usr/share/wordlists/rockyou.txt 
❯ john --show hash.txt

# fcrackzip (alternativa)
❯ fcrackzip -D -p /usr/share/wordlists/rockyou.txt -u file.zip
# -D → diccionario | -u → verificar con unzip | -p → wordlist
❯ fcrackzip -b -p /usr/share/wordlists/rockyou.txt -u file.zip
# -b → brute force

# hashcat (después de extraer con zip2john)
❯ hashcat -m 13600 hash.txt /usr/share/wordlists/rockyou.txt
# -m 13600 → WinZip AES
❯ hashcat -m 17200 hash.txt /usr/share/wordlists/rockyou.txt
# -m 17200 → PKZIP (Mixed Multi-File)
```

### SSH key con passphrase (id_rsa cifrado)
```bash
# Extraer hash
❯ ssh2john id_rsa > hash.txt
# Convierte la clave privada cifrada en hash crackeable

# john
❯ john hash.txt -w=/usr/share/wordlists/rockyou.txt 
❯ john --show hash.txt

# hashcat
❯ hashcat -m 22921 hash.txt /usr/share/wordlists/rockyou.txt
# -m 22921 → RSA/DSA/EC/OpenSSH Private Keys
```

### KeePass (.kdbx)
```bash
# Extraer hash
❯ keepass2john file.kdbx > hash.txt
# Sin archivo clave

❯ keepass2john -k keyfile.jpg file.kdbx > hash.txt
# Con archivo clave → -k especifica el archivo clave

# john
❯ john hash.txt -w=/usr/share/wordlists/rockyou.txt 
❯ john --show hash.txt

# hashcat
❯ hashcat -m 13400 hash.txt /usr/share/wordlists/rockyou.txt
# -m 13400 → KeePass 1/2 AES / KeePass 2 AES
```

* [Keepass4brute](https://github.com/r3nt0n/keepass4brute)
```bash 
# KeePass formato '40000' 
❯ ./keepass4brute.sh <kdbx-file> /usr/share/wordlists/rockyou.txt
```

### Password Safe (.psafe3)
* [PasswordSafe](https://github.com/pwsafe/pwsafe/releases?q=non-windows&expanded=true)
```bash
# Extraer hash
❯ pwsafe2john file.psafe3 > hash.txt

# john
❯ john hash.txt -w=/usr/share/wordlists/rockyou.txt 
❯ john --show hash.txt


# Descargar el .deb (ejemplo para Debian/Kali) 
❯ dpkg -i passwordsafe-debian12-1.*.deb
❯ pwsafe   # Abrir la base de datos 
	❯ apt install libykpers-1-1   # Si falta alguna librería 


NOTA: 
	- Se necesita instalar el gestor de contraseñas para mirar el contenido de la base de datos
	- Ingresar el archivo 'file.psafe3' y la password crackeada con John 
	- Dar click derecho al usuario y seleccionar 'Copy Password to Clipboard'
```

### Archivos PFX / PKCS12 (certificados)
```bash
# Crackear contraseña del PFX
❯ crackpkcs12 -d /usr/share/wordlists/rockyou.txt certificate.pfx
# -d → diccionario

# Instalar crackpkcs12 si no está disponible
❯ git clone https://github.com/crackpkcs12/crackpkcs12
❯ sudo apt install libssl-dev
❯ cd crackpkcs12 && ./configure && make && sudo make install

# Extraer contenido del PFX una vez que tienes la contraseña
❯ openssl pkcs12 -in cert.pfx -nocerts -out priv-key.pem -nodes
# Extraer clave privada → priv-key.pem

❯ openssl pkcs12 -in cert.pfx -nokeys -out certificate.pem
# Extraer certificado → certificate.pem
```

### RAR
```bash
# Extraer hash
❯ rar2john file.rar > hash.txt

# john
❯ john hash.txt -w=/usr/share/wordlists/rockyou.txt 

# hashcat
❯ hashcat -m 13000 hash.txt /usr/share/wordlists/rockyou.txt
# -m 13000 → RAR5
❯ hashcat -m 12500 hash.txt /usr/share/wordlists/rockyou.txt
# -m 12500 → RAR3
```

### PDF
```bash
# Extraer hash
❯ pdf2john file.pdf > hash.txt

# john
❯ john hash.txt -w=/usr/share/wordlists/rockyou.txt 

# hashcat
❯ hashcat -m 10500 hash.txt /usr/share/wordlists/rockyou.txt
# -m 10500 → PDF 1.4 - 1.6 (Acrobat 5-8)
❯ hashcat -m 10700 hash.txt /usr/share/wordlists/rockyou.txt
# -m 10700 → PDF 1.7 Level 8 (Acrobat 9)
```

---

## 5. WI-FI (WPA/WPA2)

```bash
# hashcat — solo en Windows con GPU para mejor rendimiento
❯ hashcat.exe -m 22000 capture.hc22000 -a 0 rockyou.txt -d 1 -D 2 -w 3
# -m 22000 → WPA-PBKDF2-PMKID+EAPOL

❯ hashcat.exe -m 22000 Hash_File -a 3 "?d?d?d?d?d?d?d?d" -d 1 -D 2 -w 3
# -a 3 → fuerza bruta | ?d → dígito | 8 dígitos = pin típico de router
```

---

## 6. ATAQUES CON REGLAS

```bash
# Las reglas generan variantes de cada palabra del diccionario
# Mayúsculas, números al final, caracteres especiales, etc.

❯ hashcat -m <modo> hash.txt /usr/share/wordlists/rockyou.txt   -r /usr/share/hashcat/rules/best66.rule
# best64 → las 64 mejores reglas → primer intento con reglas

❯ hashcat -m <modo> hash.txt /usr/share/wordlists/rockyou.txt   -r /usr/share/hashcat/rules/InsidePro-PasswordsPro.rule
# InsidePro → más completo que best64

❯ hashcat -m <modo> hash.txt /usr/share/wordlists/rockyou.txt   -r /usr/share/hashcat/rules/rockyou-30000.rule
# rockyou-30000 → reglas basadas en patrones de rockyou

❯ hashcat -m <modo> hash.txt /usr/share/wordlists/rockyou.txt   -r /usr/share/hashcat/rules/d3ad0ne.rule
# d3ad0ne → reglas avanzadas

# Crear wordlist con variantes (para usar con otras tools)
❯ hashcat --stdout -r /usr/share/hashcat/rules/best64.rule wordlist.txt > expanded.txt
# Genera todas las variantes → guarda en nuevo archivo
```

---

## 7. FUERZA BRUTA PURA

```bash
# Máscaras de hashcat:
# ?l = letra minúscula | ?u = letra mayúscula
# ?d = dígito | ?s = símbolo especial | ?a = todos

❯ hashcat -m <modo> hash.txt -a 3 ?d?d?d?d?d?d?d?d
# 8 dígitos → PINs, teléfonos

❯ hashcat -m <modo> hash.txt -a 3 ?l?l?l?l?l?l
# 6 letras minúsculas

❯ hashcat -m <modo> hash.txt -a 3 ?u?l?l?l?l?d?d
# Patrón: Mayúscula + 4 minúsculas + 2 dígitos

❯ hashcat -m <modo> hash.txt -a 3 -i --increment-min=6 --increment-max=8 ?a?a?a?a?a?a?a?a
# -i → incremento automático de longitud → de 6 a 8 caracteres

# john — fuerza bruta
❯ john --incremental hash.txt
# Modo incremental → prueba todas las combinaciones posibles
```

---

## 8. COMANDOS DE GESTIÓN

```bash
# Hashcat — ver resultados
❯ hashcat -m <modo> hash.txt --show
# Ver hashes ya crackeados en el potfile (historial)

❯ hashcat -m <modo> hash.txt --show --username
# Si el archivo tiene formato user:hash

# Hashcat — rendimiento
❯ hashcat -m <modo> hash.txt rockyou.txt -d 1 -D 2 -w 3
# -d 1 → GPU ID 1 | -D 2 → tipo dispositivo GPU | -w 3 → workload High

❯ hashcat -m <modo> hash.txt rockyou.txt --force
# --force → ignorar warnings → usar en VMs o cuando hay errores de driver

❯ hashcat -m <modo> hash.txt rockyou.txt -O
# -O → optimized kernels → más velocidad en algunos modos

# John — gestión
❯ john --show hash.txt
# Ver todos los hashes crackeados

❯ john --show --format=<formato> hash.txt
# Ver crackeados con formato específico

❯ john --list=formats
# Ver todos los formatos soportados

❯ john --list=formats | grep -i ntlm
# Buscar formato específico
```

---

## REFERENCIA RÁPIDA DE MODOS HASHCAT

| Modo | Tipo de Hash | Cuándo usarlo |
|---|---|---|
| 0 | MD5 | Hashes web, bases de datos |
| 100 | SHA1 | Hashes web |
| 400 | phpass ($P$) | WordPress, Joomla |
| 1000 | NTLM (NT hash) | SAM, secretsdump, mimikatz |
| 1400 | SHA256 | Varios |
| 1700 | SHA512 | Varios |
| 1731 | MSSQL 2012/2014 | Bases de datos |
| 3200 | bcrypt ($2y$) | Lento → wordlist pequeña |
| 5500 | NetNTLMv1 | Responder legacy |
| 5600 | NetNTLMv2 | Responder → más común |
| 13100 | Kerberoast RC4 | GetUserSPNs |
| 13400 | KeePass | Archivos .kdbx |
| 13600 | WinZip AES | Archivos .zip |
| 18200 | AS-REP ($krb5asrep$) | GetNPUsers |
| 19700 | Kerberoast AES256 | GetUserSPNs AES |
| 22000 | WPA/WPA2 | Capturas Wi-Fi |

---

## ONE-LINERS MENTALES
- Hash nuevo → identificar con hashid primero → nunca asumir
- Siempre rockyou.txt primero → si no crackea → agregar reglas best64
- NTLMv2 de Responder → hashcat -m 5600
- AS-REP de GetNPUsers → hashcat -m 18200
- Kerberoast de GetUserSPNs → hashcat -m 13100
- Archivo protegido → herramienta2john → john o hashcat
- Hash ya crackeado antes → hashcat --show para verlo sin repetir
- GPU lenta / VM → agregar --force y -w 1 para no crashear
