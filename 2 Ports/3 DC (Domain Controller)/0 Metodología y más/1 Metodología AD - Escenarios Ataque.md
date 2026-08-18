# Metodología AD — Escenarios de Ataque

Tags: #AD #ActiveDirectory #Metodologia #Kali #Windows #Dominio #Escenarios

## ESTRUCTURA DE ESTA NOTA
```
Escenario 1 → Kali sin credenciales          → ver 0_Metodologia_AD.md
Escenario 2 → Kali con credenciales vs DC    → esta nota
Escenario 3 → Kali con credenciales vs miembro del dominio → esta nota
Escenario 4 → Windows con credenciales desde el inicio    → esta nota
```

## LO PRIMERO EN CUALQUIER ESCENARIO
```
1. Agregar al /etc/hosts → IP + FQDN + hostname corto
   Standalone:  192.168.x.x  hostname
   Dominio:     192.168.x.x  dc01.domain.local  dc01

2. Sincronizar reloj con el DC → SIEMPRE antes de Kerberos
   Sin esto → KRB_AP_ERR_SKEW → todo falla silenciosamente

3. Identificar la topología → ¿cuántos DCs? ¿cuántos dominios? ¿hay trust?
   Un dominio simple vs bosque con trusts → cambia completamente el alcance
```

---

# ESCENARIO 2 — KALI CON CREDENCIALES ATACANDO UN DC

## Contexto
```
Tienes: IP del DC + user:pass (o hash NT) de un usuario de dominio
El objetivo ES el DC directamente
Todas las herramientas desde Kali → sin tocar ninguna máquina Windows
```

## TIPS DEL ESCENARIO
```
1. Cualquier usuario de dominio puede hacer Kerberoasting → empezar siempre
2. BloodHound desde Kali → no necesitas subir nada al DC
3. El DC tiene todos los puertos abiertos → muchos vectores de enumeración
4. Si comprometes el DC → comprometes todo el dominio
5. DCSync = game over → dump de todos los hashes del dominio
```

---

## E2.FASE 1 — RECONOCIMIENTO DEL DC

**Objetivo:** Entender qué servicios expone el DC y qué versión de Windows corre

```
Puertos clave a identificar en el DC:
  53   → DNS → zone transfer posible si está mal configurado
  88   → Kerberos → confirma que es DC → AS-REP y Kerberoasting
  135  → RPC → enumeración de usuarios y grupos
  139/445 → SMB → shares, null sessions, enumeración
  389/636 → LDAP/LDAPS → dump completo del directorio
  3268/3269 → Global Catalog → solo en DCs
  5985/5986 → WinRM → si las creds tienen acceso
  3389 → RDP → acceso gráfico si las creds lo permiten

¿Qué versión de Windows Server?
  → Determina qué exploits de kernel son posibles
  → Server 2019 → no JuicyPotato si llegas a escalar localmente
  → Determina vulnerabilidades conocidas del DC (PrintNightmare, ZeroLogon, etc.)

¿Hay múltiples DCs?
  → Identificar el DC primario (PDC Emulator)
  → Los cambios de contraseñas y políticas van al PDC

¿Hay trust con otros dominios?
  → Trust transitivo → puede haber path hacia otros dominios
  → Enumerar con BloodHound → ver si hay camino entre dominios
```

**→ Ver nota: Nmap.md (scripts ms-sql-info, smb-os-discovery, ldap*)**

---

## E2.FASE 2 — ENUMERACIÓN CON CREDENCIALES

**Objetivo:** Extraer toda la información posible del dominio con las credenciales iniciales

### 2.1 Validar las credenciales en todos los servicios
```
Verificar contra SMB, WinRM, LDAP, Kerberos, RDP, MSSQL si está activo
→ ¿Qué servicios acepta estas credenciales?
→ ¿Tengo [Pwn3d!] en SMB? → admin local en el DC → privilege es total
→ ¿Tengo WinRM? → shell interactiva inmediata → ir a E2.Fase 5
→ ¿Solo LDAP/Kerberos? → enumeración pero sin acceso directo al sistema
```

### 2.2 SMB — Enumeración de shares y sesiones
```
Shares accesibles con las credenciales actuales:
  SYSVOL → contiene GPOs → buscar contraseñas en GPP (Group Policy Preferences)
  NETLOGON → scripts de inicio de sesión → pueden contener credenciales hardcodeadas
  Shares personalizados → archivos de configuración, backups, bases de datos

Sesiones activas en el DC:
  → Ver qué usuarios están conectados al DC ahora mismo
  → Si hay DA conectado desde otra máquina → esa máquina es objetivo prioritario

Política de contraseñas:
  → Obtener lockout threshold antes de hacer spraying
  → Longitud mínima y complejidad → orientar el cracking
```

**→ Ver nota: 445_135_SMB_RPC.md**

### 2.3 LDAP — Dump completo del directorio
```
Con credenciales → LDAP da acceso a toda la estructura del AD:

Usuarios:
  → Lista completa con atributos → sAMAccountName, description, memberOf
  → BUSCAR SIEMPRE: descripción con contraseñas hardcodeadas (muy común)
  → Usuarios con adminCount=1 → históricamente privilegiados
  → Usuarios con passwordNotRequired → pueden no tener contraseña
  → Usuarios con passwordNeverExpires → contraseñas antiguas → más fáciles de crackear
  → Fecha del último cambio de contraseña → contraseñas viejas = más débiles
  → Usuarios deshabilitados → a veces tienen contraseñas reutilizadas en otros servicios

Grupos:
  → Domain Admins → quiénes son → objetivos prioritarios
  → Enterprise Admins → acceso a todo el bosque
  → Backup Operators → pueden leer NTDS.dit
  → Account Operators → pueden crear y modificar cuentas
  → DNSAdmins → pueden cargar DLL maliciosa en el DNS del DC
  → Grupos anidados → un grupo puede ser miembro de otro → revisar cadenas

Computadoras:
  → Lista de todos los hosts del dominio → objetivos para lateral movement
  → Versión del OS de cada host → identificar hosts vulnerables
  → Última vez que el host hizo login → hosts inactivos = más fáciles

GPOs:
  → Group Policy Preferences → pueden contener contraseñas cifradas con clave conocida (AES)
  → SYSVOL → buscar archivos XML con cpassword → descifrar automáticamente
```

**→ Ver nota: 389_LDAP.md**

### 2.4 RPC — Enumeración adicional
```
Con credenciales → RPC da más información que null session:
  → enumdomusers → lista completa de usuarios con RIDs
  → queryuser → info detallada por RID incluyendo descripción
  → enumdomgroups → grupos y sus RIDs
  → querygroupmem → miembros de un grupo específico
  → enumprivs → privilegios disponibles en el dominio
  → netshareenumall → todos los shares incluyendo los ocultos (con $)
```

### 2.5 DNS — Enumeración del dominio
```
El DNS del DC contiene todos los hosts del dominio:
  → Zone transfer → si está habilitado → todos los registros de una vez
  → Registros SRV → identificar todos los DCs, servidores de Exchange, etc.
  → Registros A → IPs de todos los hosts
  → Registros CNAME → aliases → pueden revelar servicios ocultos
  → Subzonas → si hay subdominios con registros propios
```

**→ Ver nota: 53_DNS.md**

---

## E2.FASE 3 — BLOODHOUND (MAPA COMPLETO DEL DOMINIO)

**Objetivo:** Obtener el mapa visual de todas las relaciones y paths hacia DA

```
Recolección desde Kali con bloodhound-python:
  → No requiere subir nada al DC
  → Usa las credenciales para consultar LDAP y SMB
  → Recolectar: usuarios, grupos, computadoras, sesiones, ACLs, GPOs, trusts
  → Modo All → el más completo → úsalo siempre

Análisis prioritario en BloodHound:
  1. Marcar usuario inicial como "Owned"
  2. Shortest Path to Domain Admins → ¿cuántos saltos hay?
  3. Find Principals with DCSync Rights → quién puede hacer DCSync
     → Si tu usuario tiene DCSync → ir a E2.Fase 7 directamente
  4. Kerberoastable Users → cuentas con SPNs → targets de cracking
  5. AS-REP Roastable Users → sin preauth → targets sin necesitar creds
  6. Node Info de tu usuario:
     → Outbound Object Control → qué objetos puedes controlar directamente
     → Group Membership → a qué grupos perteneces → aprovechar pertenencia
  7. Find Shortest Path from Owned Principals → paths desde lo que ya tienes
  8. Transitive Object Control → control indirecto a través de cadenas de ACLs

Buscar en BloodHound específicamente:
  → ¿Pertenezco a algún grupo con privilegios especiales?
  → ¿Tengo GenericAll o WriteDACL sobre algún usuario/grupo/DC?
  → ¿Hay cuentas de servicio con SPNs en grupos privilegiados?
  → ¿Hay trust con otros dominios? → paths cross-domain
```

**→ Ver nota: BloodHound.md**

---

## E2.FASE 4 — KERBEROASTING Y AS-REP ROASTING

**Objetivo:** Obtener hashes crackeables sin necesitar más privilegios

```
Kerberoasting:
  → Cualquier usuario de dominio puede hacer esto → no necesitas ser admin
  → Buscar cuentas de servicio con SPNs → solicitar TGS → hash crackeable
  → Priorizar cuentas que estén en grupos privilegiados → ver en BloodHound
  → RC4 (tipo 23) → más fácil de crackear que AES
  → Cuentas de servicio suelen tener contraseñas débiles → alta tasa de crackeo
  → Si crackeas → tienes creds de cuenta de servicio → puede ser admin en múltiples hosts

AS-REP Roasting:
  → No requiere credenciales para las cuentas sin preauth
  → Con credenciales → enumeras TODOS los usuarios vulnerables
  → Hash krb5asrep → crackear con hashcat -m 18200
  → Si crackeas → creds de un usuario de dominio → alimenta las siguientes fases

Hash crackeado → probar inmediatamente en:
  → SMB de todos los hosts de la subred → ver dónde tiene acceso
  → WinRM → intentar shell interactiva
  → RDP → acceso gráfico
  → MSSQL → si hay servidor SQL
```

**→ Ver notas: 88_Kerberos.md / Password_Cracking.md**

---

## E2.FASE 5 — PASSWORD SPRAYING

**Objetivo:** Encontrar usuarios con contraseñas débiles o comunes

```
ANTES de sprayear → verificar política de lockout:
  → Lockout threshold → cuántos intentos antes de bloquear
  → Lockout duration → cuánto tiempo dura el bloqueo
  → Reset counter → cuándo se reinicia el contador
  → Si lockout = 0 → sin lockout → puedes ser más agresivo
  → Regla de oro → máximo 1 password por usuario por ronda → respetar siempre

Contraseñas a probar en orden:
  → Contraseñas de temporada: Winter2024, Summer2024, Spring2024
  → Nombre de la empresa + año: CompanyName2024, CompanyName!
  → Contraseñas comunes: Password123, Welcome1, Passw0rd
  → Nombre del dominio + año: Domain2024
  → Mes + año: March2024, Marzo2024
  → Blank password → algunos usuarios pueden tener contraseña vacía

Via Kerberos → más sigiloso → menos logs en el DC que via SMB
Via LDAP → también funciona → menos común en herramientas
Via SMB → funciona pero genera más logs de eventos → más ruido

Usuarios prioritarios para el spray:
  → Usuarios de servicio → contraseñas simples establecidas por admins
  → Usuarios con passwordNeverExpires → contraseñas muy antiguas → débiles
  → Usuarios con lastPasswordSet muy antigua → puede ser la original del admin
```

**→ Ver nota: 88_Kerberos.md / Hydra.md**

---

## E2.FASE 6 — ACL ABUSE

**Objetivo:** Aprovechar permisos mal configurados sobre objetos AD para escalar

```
BloodHound → identificar edges abusables desde tu usuario o desde owned principals:

GenericAll sobre usuario:
  → Cambiar su contraseña sin conocer la actual
  → Añadirlo a grupos privilegiados
  → Configurar SPN → Kerberoast targeted

GenericAll sobre grupo:
  → Añadirte al grupo directamente
  → Si el grupo es Domain Admins → DA inmediato

ForceChangePassword:
  → Cambiar contraseña del usuario objetivo
  → Sin necesitar conocer la contraseña actual

WriteDACL sobre el dominio:
  → Darte permisos de DCSync
  → Una vez con DCSync → dump de todos los hashes → game over

GenericWrite sobre usuario:
  → Modificar atributos del usuario
  → Agregar SPN → Kerberoast targeted
  → Modificar scriptPath → ejecutar script al login

AllExtendedRights:
  → Incluye ForceChangePassword y otras operaciones extendidas
  → Leer atributos sensibles como laps password

WriteOwner sobre objeto:
  → Tomar propiedad del objeto
  → Una vez dueño → WriteDACL → cualquier permiso

AddMember sobre grupo:
  → Añadir cualquier usuario al grupo
  → Añadirte a ti mismo o a un usuario que controlas

Después de cada abuso:
  → Verificar que el cambio se aplicó
  → Probar las nuevas credenciales/permisos inmediatamente
  → Volver a BloodHound si no funciona → buscar otro path
```

**→ Ver nota: ACL_Abuse_AD.md / 464_kpasswd.md**

---

## E2.FASE 7 — DCSYNC (COMPROMETER EL DOMINIO)

**Objetivo:** Extraer todos los hashes del dominio → control total

```
Condición necesaria:
  → Tener permisos DS-Replication-Get-Changes Y DS-Replication-Get-Changes-All
  → Domain Admins y Enterprise Admins los tienen por defecto
  → WriteDACL sobre el dominio → darte esos permisos → DCSync

Desde Kali → directamente contra el DC:
  → impacket-secretsdump → no necesitas estar dentro del DC
  → Extrae: hashes NT de todos los usuarios, historial de contraseñas, hashes Kerberos

Hashes de valor crítico:
  Administrator → Pass-the-Hash → admin local en TODOS los hosts del dominio
  krbtgt → Golden Ticket → persistencia indefinida → tickets válidos por 10 años
  Otros DA → redundancia → si cambian la clave del admin, tienes otros

Con hash de Administrator:
  → PTH a cualquier máquina del dominio → acceso garantizado
  → Dump local de SAM en cada máquina → más credenciales
  → Buscar credenciales de otros servicios → bases de datos, VPN, etc.

Con hash de krbtgt → Golden Ticket:
  → Crear tickets Kerberos para cualquier usuario
  → Persistencia incluso si cambian contraseñas del dominio
  → El ticket dura hasta que cambien la clave de krbtgt dos veces

Verificar si ya tienes permisos de DCSync ANTES de intentarlo:
  → BloodHound → Find Principals with DCSync Rights
  → Si apareces → ejecutar directamente
  → Si no apareces → necesitas escalar o abusar ACL primero
```

**→ Ver nota: DCSync.md**

---

## E2.FLUJO DE DECISIÓN

```
Kali + credenciales vs DC
│
├── Validar creds → ¿[Pwn3d!] en SMB? → admin en DC → DCSync directo
│
├── WinRM disponible → shell en DC → mimikatz → DCSync local
│
├── BloodHound → ¿path corto hacia DA?
│     ├── DCSync rights → secretsdump → game over
│     ├── ACL abusable → explotar → escalar → volver a evaluar
│     └── Kerberoastable DA group member → crackear → DA creds
│
├── Kerberoasting → cuentas de servicio → crackear → ¿admin en algún host?
│
├── AS-REP Roasting → cuentas sin preauth → crackear → más creds
│
├── Password Spraying → usuarios con pass débil → más creds
│
└── Credenciales nuevas → volver al inicio → revaluar con nuevos permisos
```

---

# ESCENARIO 3 — KALI CON CREDENCIALES ATACANDO MÁQUINA MIEMBRO DEL DOMINIO

## Contexto
```
Tienes: IP de un servidor miembro (no el DC) + user:pass de dominio
El servidor puede ser: IIS, MSSQL, file server, workstation, etc.
El objetivo final SIGUE siendo el DC → este servidor es el trampolín
```

## TIPS DEL ESCENARIO
```
1. El servidor miembro tiene menos puertos que el DC → más focalizado
2. Entrar al servidor → escalar localmente → credenciales de dominio → DC
3. El usuario de servicio local (IIS, SQL) casi siempre tiene SeImpersonatePrivilege
4. Credenciales encontradas en el servidor → pueden funcionar en el DC o en otros hosts
5. Siempre enumerar qué usuarios de dominio han logueado en este servidor → mimikatz
```

---

## E3.FASE 1 — RECONOCIMIENTO DEL SERVIDOR MIEMBRO

**Objetivo:** Identificar qué servicios expone y cómo entrar

```
Puertos típicos en servidores miembro:
  80/443  → Web (IIS, Apache) → upload de webshells, exploits de CMS
  445     → SMB → shares, credenciales, null session
  1433    → MSSQL → sa sin contraseña, xp_cmdshell, impersonación
  3389    → RDP → acceso gráfico con las credenciales
  5985    → WinRM → shell interactiva directa
  8080/8443 → Tomcat, aplicaciones web → exploits específicos
  21      → FTP → acceso anónimo, subida de webshells

Identificar el rol del servidor:
  → Web server (IIS) → upload de webshell ASPX → RCE
  → SQL server → MSSQL → xp_cmdshell → RCE
  → File server → shares accesibles → buscar credenciales en archivos
  → Application server → explotar la aplicación web
  → Workstation → usuario logueado → credenciales en memoria

¿Qué versión del OS?
  → Determina qué Potato attack usar si llegas a tener SeImpersonatePrivilege
  → Determina si hay exploits de kernel disponibles
  → Server 2019 → PrintSpoofer o GodPotato
  → Server 2016 → JuicyPotato o PrintSpoofer
```

---

## E3.FASE 2 — ENTRAR AL SERVIDOR

**Objetivo:** Obtener acceso al servidor con las credenciales disponibles

### 2.1 Acceso directo con las credenciales
```
WinRM (5985) → evil-winrm → shell interactiva → más cómoda para trabajar
SMB (445) → ¿[Pwn3d!]? → admin local → psexec → SYSTEM
RDP (3389) → acceso gráfico → más cómodo para algunos usuarios
MSSQL (1433) → xp_cmdshell → RCE si eres sysadmin

¿Las credenciales son de dominio o locales?
  → Dominio → probar en todos los servicios
  → Locales → solo funcionan en ese servidor específico
  → Hash NT → PTH en SMB/WinRM → sin necesitar contraseña en claro
```

### 2.2 Acceso via vulnerabilidad de servicio
```
Si las credenciales no dan acceso directo → buscar vulnerabilidad en el servicio:

IIS expuesto:
  → Versión vulnerable → searchsploit
  → WebDAV habilitado → subir webshell ASPX directamente
  → Aplicación web → SQLi, LFI, File Upload, RCE
  → Directorio de subidas → ¿accesible con las credenciales SMB?

MSSQL expuesto:
  → Probar sa sin contraseña → muy común
  → Credenciales de dominio con -windows-auth → puede ser sysadmin
  → Impersonación → actuar como SA sin serlo → xp_cmdshell
  → Linked servers → RCE en otro servidor SQL

SMB expuesto:
  → Shares accesibles → buscar archivos con credenciales
  → SYSVOL → GPP passwords → cifrado con clave conocida → descifrar
  → Shares de scripts → scripts con credenciales hardcodeadas
```

**→ Ver notas: 80_HTTP.md / 1433_MSSQL.md / 445_135_SMB_RPC.md**

---

## E3.FASE 3 — ENUMERACIÓN DENTRO DEL SERVIDOR

**Objetivo:** Entender el contexto local y buscar vectores de escalada

```
Una vez dentro como usuario de dominio normal:

Contexto del usuario:
  → whoami /priv → ¿SeImpersonatePrivilege? → Potato inmediato
  → whoami /groups → grupos locales y de dominio → roles especiales
  → net localgroup administrators → ¿ya soy admin local?

Otros usuarios en el sistema:
  → query user → ¿hay usuarios de dominio logueados ahora?
     → DA logueado → ese servidor es el objetivo prioritario → mimikatz
  → net user → usuarios locales configurados → posibles creds reutilizadas

Credenciales en el servidor:
  → Credential Manager → credenciales guardadas de otros usuarios
  → Historial de PowerShell → comandos ejecutados con credenciales
  → Archivos de configuración → web.config, applicationHost.config, .env
  → Registry → Winlogon → autologon con usuario de dominio
  → Unattend.xml → instalación desatendida → credenciales en base64
  → Scripts en SYSVOL montado → credenciales hardcodeadas

Servicios que corren en el servidor:
  → ¿Con qué usuario corre IIS? → NT Authority\Network Service → SeImpersonate
  → ¿Con qué usuario corre MSSQL? → NT Service\MSSQLSERVER → SeImpersonate
  → Servicios de dominio → cuenta de servicio → credenciales valiosas

Conectividad de red:
  → ¿Hay más interfaces? → otras subredes → más hosts alcanzables
  → ¿Puede llegar al DC? → lateral movement directo
  → ¿Hay otros servidores internos no visibles desde Kali?
```

**→ Ver notas: WinPEAS.md / PowerUp.md / Windows_Comandos.md**

---

## E3.FASE 4 — ESCALAR LOCALMENTE A SYSTEM

**Objetivo:** Obtener SYSTEM en el servidor miembro

```
Seguir el mismo orden que la metodología de PrivEsc standalone:

Prioridad 1 → SeImpersonatePrivilege:
  → Casi siempre presente en usuarios de servicio (IIS, MSSQL)
  → Potato attack según la versión del OS
  → Server 2019/Win10 post-1809 → PrintSpoofer o GodPotato
  → Server 2016/Win10 pre-1809 → JuicyPotato
  → SYSTEM garantizado en minutos

Prioridad 2 → Servicios mal configurados:
  → winPEAS + PowerUp → detectar automáticamente
  → Unquoted Service Path → plantar ejecutable → reiniciar servicio
  → Weak Service Permissions → modificar binPath → SYSTEM
  → AlwaysInstallElevated → MSI malicioso → SYSTEM

Prioridad 3 → Credenciales encontradas:
  → Si encontraste creds de admin local → usarlas directamente
  → Si el admin local tiene la misma contraseña en el DC → PTH al DC

→ SYSTEM obtenido → ir a E3.Fase 5
→ Ver nota: 0_Metodologia_Windows_PrivEsc_Standalone.md
```

---

## E3.FASE 5 — DESDE SYSTEM: EXTRAER CREDENCIALES DE DOMINIO

**Objetivo:** Obtener credenciales de dominio para moverse al DC

```
Con SYSTEM en el servidor miembro:

Mimikatz → credenciales en memoria:
  → sekurlsa::logonpasswords → contraseñas en claro y hashes de sesiones activas
  → Si hay DA logueado → sus credenciales aparecen aquí → DA inmediato
  → sekurlsa::tickets → tickets Kerberos en memoria → Pass-the-Ticket
  → lsadump::sam → hashes locales → pueden reutilizarse en otros hosts
  → lsadump::secrets → LSA Secrets → credenciales de servicios de dominio

secretsdump remoto desde Kali:
  → Con las creds del admin local → dump remoto sin subir nada
  → SAM → hashes locales
  → LSA Secrets → creds de servicios → cuentas de dominio con contraseña
  → Cached credentials → hashes de usuarios de dominio que loguearon antes
  → Crackear los cached hashes → contraseñas de usuarios de dominio

Cuenta de máquina HOSTNAME$:
  → Con SYSTEM puedes actuar como HOSTNAME$ en el dominio
  → HOSTNAME$ es cuenta de dominio básica → puede hacer Kerberoasting
  → Puede ejecutar SharpHound → recolectar datos del dominio

¿Credenciales de dominio encontradas?
  → Probar inmediatamente en el DC → SMB, WinRM, RDP
  → Si son de DA → DCSync → comprometer el dominio
  → Si son de usuario normal → volver a E2 con esas creds
```

---

## E3.FASE 6 — MOVERSE AL DC (LATERAL MOVEMENT)

**Objetivo:** Usar las credenciales de dominio para comprometer el DC

```
Con credenciales de dominio obtenidas del servidor miembro:

Validar acceso al DC:
  → nxc smb DC_IP -u user -p pass → ¿[Pwn3d!]?
  → nxc winrm DC_IP -u user -p pass → ¿acceso WinRM?
  → Si [Pwn3d!] → admin en el DC → DCSync → game over

Si las creds son de DA:
  → evil-winrm al DC → shell interactiva
  → impacket-psexec al DC → SYSTEM en el DC
  → impacket-secretsdump al DC → todos los hashes

Si las creds son de usuario normal:
  → Volver a E2 con las credenciales nuevas
  → BloodHound desde Kali → ver qué puede hacer este usuario
  → Kerberoasting, ACL abuse, etc.

Si tenemos hash pero no contraseña:
  → Pass-the-Hash → nxc smb DC_IP -u user -H NThash
  → PTH funciona en SMB y WinRM incluso sin contraseña en claro

Desde el servidor miembro como trampolín:
  → Si el DC no es accesible desde Kali directamente
  → Port forwarding desde el servidor miembro → alcanzar el DC
  → Chisel desde Kali → cliente en el servidor → SOCKS proxy
  → Toda la red interna accesible a través del túnel
```

**→ Ver nota: Lateral_Movement.md**

---

## E3.FLUJO DE DECISIÓN

```
Kali + credenciales vs servidor miembro
│
├── Validar creds → ¿acceso directo?
│     ├── WinRM → shell → enumerar → escalar
│     ├── [Pwn3d!] SMB → ya soy admin local → mimikatz → creds dominio
│     ├── MSSQL → xp_cmdshell → RCE → escalar
│     └── Web → vulnerabilidad → webshell → RCE
│
├── Dentro del servidor
│     ├── whoami /priv → SeImpersonatePrivilege → Potato → SYSTEM
│     ├── query user → ¿DA logueado? → objetivo prioritario → mimikatz
│     └── winPEAS → vector local → SYSTEM
│
├── Con SYSTEM → mimikatz → credenciales de dominio
│     ├── DA encontrado → PTH/evil-winrm al DC → DCSync
│     ├── Usuario normal → BloodHound → paths
│     └── Solo HOSTNAME$ → Kerberoasting → crackear → más creds
│
└── Con creds de dominio → volver a E2 con esas creds
```

---

# ESCENARIO 4 — WINDOWS CON CREDENCIALES DEL DOMINIO DESDE EL INICIO

## Contexto
```
Te sientan frente a una máquina Windows unida al dominio
Ya tienes credenciales → el usuario está logueado o te los dan
No hay fase de entrada → estás dentro desde el primer momento
Tienes acceso a herramientas nativas de Windows y puedes subir otras
```

## TIPS DEL ESCENARIO
```
1. Verificar primero qué eres → local vs dominio → cambia el enfoque
2. SharpHound desde el inicio → mientras corre → enumerar manualmente
3. PowerView → mucho más potente que los comandos nativos
4. Find-DomainUserLocation → encontrar DAs logueados → objetivo prioritario
5. Si eres admin local → mimikatz → credenciales en memoria ahora mismo
6. Las herramientas pueden detectarse → cargarlas en memoria con IEX cuando sea posible
```

---

## E4.FASE 1 — CONTEXTO INICIAL

**Objetivo:** Entender exactamente quién eres y dónde estás

```
Identidad:
  → whoami → ¿DOMAIN\user o HOSTNAME\user?
  → whoami /priv → SeImpersonatePrivilege → Potato si hay IIS/SQL corriendo
  → whoami /groups → grupos de dominio → buscar grupos privilegiados
  → echo %LOGONSERVER% → qué DC me autenticó → IP del DC

Máquina:
  → hostname → nombre del equipo
  → systeminfo → OS, versión, parches instalados
  → ipconfig /all → interfaces → ¿hay más subredes?
  → ¿Es workstation o server? → workstation = usuario logueado = credenciales en memoria

Dominio:
  → systeminfo | findstr Domain → nombre del dominio
  → net group "Domain Admins" /domain → quiénes son los DA → objetivos
  → net accounts /domain → política de contraseñas → para spraying

¿Soy admin local?
  → net localgroup administrators → ¿aparezco?
  → SÍ → mimikatz → credenciales de sesiones activas → muy valioso
  → NO → buscar escalar primero → ver E4.Fase 3
```

---

## E4.FASE 2 — ENUMERACIÓN DEL DOMINIO

**Objetivo:** Recolectar toda la información del AD desde la máquina Windows

### 2.1 SharpHound → ejecutar inmediatamente
```
Lanzar SharpHound al inicio → tarda un tiempo → mientras corre → enumerar manualmente
  → Modo All → más completo
  → Genera .zip → transferir a Kali → importar en BloodHound

Análisis en BloodHound:
  → Mismo proceso que en E2.Fase 3
  → Especialmente útil: Find-DomainUserLocation → DAs logueados ahora
  → Outbound Object Control de tu usuario → ACLs abusables
```

### 2.2 PowerView → enumeración avanzada
```
Cargar en memoria → evitar que el AV lo detecte en disco

Usuarios:
  → Get-DomainUser con todos sus atributos
  → BUSCAR: usuarios con descripción que contenga pass/pwd/key/secret
  → Get-DomainUser -SPN → Kerberoasting targets
  → Get-DomainUser -PreauthNotRequired → AS-REP targets
  → Usuarios con passwordNeverExpires → contraseñas antiguas

Grupos:
  → Get-DomainGroupMember "Domain Admins" → quiénes son exactamente
  → Grupos anidados → puede haber paths indirectos hacia DA
  → ¿Soy miembro de algún grupo privilegiado? → explotar directamente

Sesiones activas → MUY IMPORTANTE:
  → Find-DomainUserLocation -GroupName "Domain Admins"
  → Ver en qué hosts están logueados los DAs AHORA MISMO
  → Si hay DA logueado en esta máquina → mimikatz → sus creds
  → Si hay DA logueado en otro host → ese host es el objetivo prioritario

ACLs:
  → Find-InterestingDomainAcl → buscar ACLs abusables en todo el dominio
  → ¿Tienes GenericAll, WriteDACL, ForceChangePassword sobre algún objeto?

Shares accesibles:
  → Find-DomainShare -CheckShareAccess → shares donde tengo lectura/escritura
  → Buscar archivos con credenciales en shares accesibles
```

### 2.3 Enumeración nativa de Windows
```
Para cuando no quieres usar herramientas externas:
  → net user /domain → todos los usuarios
  → net group /domain → todos los grupos
  → net group "Domain Admins" /domain → miembros de DA
  → net view /domain → todos los hosts
  → ([adsisearcher]"(objectClass=user)").FindAll() → ADSI directo sin módulos
  → wmic useraccount list brief → usuarios locales
  → query user → sesiones activas en este equipo
```

---

## E4.FASE 3 — ESCALAR LOCALMENTE (SI NO SOY ADMIN)

```
Si no soy admin local → necesito escalar antes de poder usar mimikatz

Seguir la metodología de PrivEsc Windows:
  → winPEAS + PowerUp → detectar vectores automáticamente
  → whoami /priv → SeImpersonatePrivilege → Potato
  → Servicios mal configurados → Unquoted Path, Weak Permissions
  → AlwaysInstallElevated → MSI malicioso
  → Credenciales encontradas en el sistema → autologon, historial PS

→ SYSTEM/Admin obtenido → ir a E4.Fase 4
→ Ver nota: 0_Metodologia_Windows_PrivEsc_Standalone.md
→ Ver nota: 0_Metodologia_Windows_PrivEsc_Dominio.md
```

---

## E4.FASE 4 — EXTRAER CREDENCIALES (COMO ADMIN/SYSTEM)

**Objetivo:** Extraer todas las credenciales disponibles en la máquina

```
Con privilegios de admin/SYSTEM → mimikatz:

Sesiones activas → máxima prioridad:
  → sekurlsa::logonpasswords → contraseñas en claro y hashes de todos los logueados
  → Si hay DA logueado → sus credenciales → DA inmediato
  → Hashes NT → PTH en cualquier otro host del dominio

Tickets Kerberos en memoria:
  → sekurlsa::tickets → dump de tickets activos
  → Si hay ticket de DA → Pass-the-Ticket → actuar como DA
  → Exportar tickets → usar desde Kali con KRB5CCNAME

Hashes locales SAM:
  → lsadump::sam → hashes de usuarios locales
  → Pueden reutilizarse en otros hosts del dominio (misma contraseña)

LSA Secrets:
  → lsadump::secrets → credenciales de servicios de dominio
  → Cuentas de servicio → contraseñas en claro
  → Hash de la cuenta de máquina HOSTNAME$

Credential Manager:
  → cmdkey /list → credenciales guardadas
  → Pueden ser de DAs u otros usuarios privilegiados

Historial de PowerShell → credenciales usadas en comandos anteriores
Archivos de configuración → web.config, scripts, .env, unattend.xml
```

**→ Ver nota: Mimikatz.md**

---

## E4.FASE 5 — KERBEROASTING Y AS-REP DESDE WINDOWS

```
Con cualquier usuario de dominio válido:

Kerberoasting con Rubeus:
  → Invoke-Kerberoast (PowerView) → hashes en formato hashcat
  → Rubeus kerberoast → más completo y con más opciones
  → Transferir hashes a Kali → crackear con hashcat -m 13100
  → Priorizar cuentas en grupos privilegiados → ver BloodHound

AS-REP Roasting con Rubeus:
  → Rubeus asreproast → hashes krb5asrep
  → Transferir a Kali → hashcat -m 18200

Hash crackeado → validar inmediatamente en SMB/WinRM/RDP de todos los hosts
```

---

## E4.FASE 6 — ACL ABUSE DESDE WINDOWS

```
BloodHound identificó ACL abusable → ejecutar desde Windows:

Con PowerView:
  → ForceChangePassword → Set-DomainUserPassword → nueva contraseña
  → GenericAll sobre grupo → Add-DomainGroupMember → añadirte al grupo
  → WriteDACL → Add-DomainObjectAcl → darte DCSync rights

Con herramientas nativas:
  → net group "Domain Admins" miusuario /add → si tienes AddMember

Verificar que funcionó:
  → Get-DomainGroupMember "Domain Admins" → ¿aparezco?
  → BloodHound → actualizar → ver nuevos paths
  → Probar nuevas credenciales/permisos inmediatamente
```

---

## E4.FASE 7 — LATERAL MOVEMENT DESDE WINDOWS

**Objetivo:** Usar las credenciales obtenidas para moverse a otros hosts

```
Con credenciales en claro:
  → Enter-PSSession → shell remota via WinRM
  → Invoke-Command → ejecutar comandos remotos sin shell interactiva
  → PsExec → shell como SYSTEM en el host remoto

Con hash NT → Pass-the-Hash:
  → Mimikatz sekurlsa::pth → abrir proceso como el usuario del hash
  → Desde esa ventana → net use, Enter-PSSession, etc.

Con ticket Kerberos → Pass-the-Ticket:
  → Rubeus ptt → inyectar ticket en la sesión actual
  → klist → verificar ticket activo
  → Desde esa sesión → acceder a recursos del dominio como ese usuario

Prioridad de targets:
  → DC → si tienes creds de DA → DCSync → comprometer dominio
  → Hosts con DA logueado → Find-DomainUserLocation → mimikatz → DA creds
  → Hosts con [Pwn3d!] → admin local → más credenciales
```

---

## E4.FASE 8 — DCSYNC DESDE WINDOWS

```
Si llegaste a DA o tienes permisos de replicación:

Con mimikatz:
  → lsadump::dcsync /domain:domain.local /user:Administrator
  → lsadump::dcsync /domain:domain.local /all /csv → todos los hashes
  → lsadump::dcsync /domain:domain.local /user:krbtgt → Golden Ticket

Hash de Administrator:
  → PTH a cualquier máquina del dominio
  → Acceso garantizado incluso si cambian contraseñas

Hash de krbtgt:
  → Golden Ticket → persistencia indefinida
  → Tickets válidos aunque cambien contraseñas de DAs

Desde Kali con hashes obtenidos:
  → impacket-secretsdump con hash de DA → dump completo remoto
  → nxc smb subred/24 -u Administrator -H hash → ver acceso a todos los hosts
```

---

## E4.FLUJO DE DECISIÓN

```
Windows + credenciales de dominio
│
├── whoami /groups → ¿ya soy DA o grupo privilegiado?
│     └── SÍ → mimikatz → DCSync → game over
│
├── whoami /priv → SeImpersonatePrivilege?
│     └── SÍ → Potato → SYSTEM → mimikatz → creds → DA
│
├── SharpHound → BloodHound → path hacia DA
│     ├── DCSync rights → secretsdump → game over
│     ├── ACL abusable → PowerView → explotar → volver a evaluar
│     └── Kerberoastable en grupo privilegiado → crackear → DA
│
├── Find-DomainUserLocation → ¿DA logueado en algún host?
│     └── SÍ → ese host es el objetivo → moverse → mimikatz → DA creds
│
├── Soy admin local → mimikatz → credenciales en memoria
│     └── ¿Hay DA logueado aquí? → sus creds → DA inmediato
│
├── Kerberoasting + AS-REP → crackear → más creds → revaluar
│
└── Password Spraying → usuario con pass débil → más superficie
```

---

## TABLA COMPARATIVA DE LOS 3 ESCENARIOS

```
                    E2 (Kali vs DC)    E3 (Kali vs Miembro)    E4 (Windows)
────────────────────────────────────────────────────────────────────────────
Punto de entrada    Remoto al DC       Remoto al servidor       Ya dentro
Herramientas        Impacket/nxc       Impacket/nxc             PowerView/Rubeus
BloodHound          bloodhound-python  bloodhound-python        SharpHound
Kerberoasting       GetUserSPNs        GetUserSPNs              Invoke-Kerberoast/Rubeus
PrivEsc local       No necesario       Casi siempre necesario   Puede ser necesario
Creds dominio       Ya las tienes      Las extraes del server   Las extraes localmente
DCSync              secretsdump        secretsdump remoto       Mimikatz lsadump::dcsync
Objetivo final      Comprometer DC     DC via trampolín         Comprometer DC
```

---

## REFERENCIAS

```
0_Metodologia_AD.md                         → Escenario 1 (sin credenciales)
0_Metodologia_Windows_PrivEsc_Standalone.md → PrivEsc local en Windows
0_Metodologia_Windows_PrivEsc_Dominio.md    → PrivEsc en contexto de dominio
AD_Punto_Entrada_Kali_vs_Windows.md         → Comandos por punto de entrada
Enumeracion_Usuarios_Windows_AD.md          → Todos los vectores de enumeración
88_Kerberos.md                              → Kerberos, tickets, roasting
389_LDAP.md                                 → Enumeración LDAP
445_135_SMB_RPC.md                          → SMB y RPC
BloodHound.md                               → Análisis del dominio
ACL_Abuse_AD.md                             → Abuso de permisos AD
Lateral_Movement.md                         → Movimiento lateral
DCSync.md                                   → Dump de hashes
Mimikatz.md                                 → Extracción de credenciales
Password_Cracking.md                        → Crackeo de hashes
464_kpasswd.md                              → Cambio de contraseñas AD
```