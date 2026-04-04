# Metodología Active Directory

Tags: #AD #Metodologia #DC #Flujo #BloodHound #Kerberos #SMB #LDAP

## OBJETIVO

Tener el mapa claro de ataque desde cero hasta Domain Admin.
Saber exactamente qué hacer en cada fase según lo que tengas.
Los comandos están en las notas individuales de cada puerto.
Esta nota = Escenario 1 → sin credenciales desde Kali
Para otros escenarios → ver 0_1_Metodologia_AD_Escenarios.md


## TIPS

1. Enumera más de lo que crees necesario antes de atacar
2. Cada credencial nueva → pruébala en TODOS los servicios inmediatamente
3. Anota cada usuario, hash y ticket que encuentres → todo se reutiliza
4. Si te atascas → vuelve a enumerar, no a atacar más fuerte
5. BloodHound primero cuando tengas creds → te dice el camino antes de buscarlo a ciegas
6. Responder desde el inicio → completamente pasivo → puede darte creds gratis


---

## FASE 1 — RECONOCIMIENTO (SIN CREDS)

**Objetivo:** Confirmar que es un DC, mapear toda la topología, preparar el entorno y activar captura pasiva

```
ANTES DE EMPEZAR → preparar el entorno:
  → Agregar al /etc/hosts → IP + FQDN + hostname corto
     DC:              192.168.x.x  dc01.domain.local  dc01  domain.local
     Miembro dominio: 192.168.x.x  server01.domain.local server01
     Sin esto → Kerberos y LDAP fallan silenciosamente
  → Sincronizar reloj con el DC → SIEMPRE antes de Kerberos
     Sin esto → KRB_AP_ERR_SKEW → todos los ataques Kerberos fallan
     Kerberos tolera máximo 5 minutos de diferencia
```

1. **Escaneo de puertos completo**
    
    - ¿Puertos 88 + 389 + 445 + 3268 abiertos? → confirmado DC
    - ¿Puerto 88 abierto en otras IPs de la subred? → hay más DCs → mapearlos todos → identificar el PDC Emulator
    - ¿Puertos 445, 5985, 80, 1433 en otras IPs? → hay servidores miembro → posibles trampolines hacia el DC
    - ¿Versión del OS del DC? → Server 2019/2022 vs 2016/2012 → define exploits disponibles
    - ¿Puertos 3268/3269 en múltiples IPs? → posible bosque con múltiples dominios → BloodHound mostrará los trusts
    - **→ Ver nota: Nmap.md**
2. **Identificar hostname y dominio**
    
    - Via SMB (nxc) → hostname, dominio y versión del OS en una sola consulta
    - Via LDAP (ldapsearch) → base DN del dominio
    - Via DNS → registros SRV → lista completa de todos los DCs
    - Via nmap smb-os-discovery → hostname, FQDN, NetBIOS, dominio
    - Anotar: nombre corto (domain01), FQDN (domain01.local.local), hostname DC, IP DC
3. **Activar Responder** ← activar ahora y dejar corriendo toda la sesión
    
    - Completamente pasivo → no genera ruido → no hay razón para no activarlo
    - Captura hashes NTLMv2 cuando un host intenta resolver un nombre inexistente:
        - Scripts o tareas programadas apuntando a recursos inexistentes → muy común en labs
        - Bots configurados con recursos de red 
        - Usuarios escribiendo mal nombres de shares → autentica contra Kali automáticamente
    - → Hash capturado → nombre de usuario revelado + crackear offline con hashcat -m 5600
    - → Hash crackeado → credenciales en claro → **ir a Fase 3 directamente**
    - **→ Ver nota: LLMNR_Responder.md**

> **Fase 1 completa → continuar con Fase 2 → enumeración activa sin credenciales**

---

## FASE 2 — ENUMERACIÓN SIN CREDENCIALES

**Objetivo:** Conseguir usuarios válidos o credenciales para pasar a Fase 3

```
El orden importa → de menos ruido a más ruido
Guardar TODOS los usuarios encontrados en un archivo → se reutilizan en AS-REP y spraying
```

1. **SMB null session**
    
    - ¿Hay shares accesibles sin creds? → listar contenido → buscar archivos con credenciales
    - ¿Puedo enumerar usuarios y grupos via RID brute? → guardar lista
    - → Sí a cualquiera: guardar hallazgos → continuar a paso 5 (AS-REP con los usuarios)
    - → No: continuar
    - **→ Ver nota: 445_135_SMB_RPC.md**
2. **RPC null session**
    
    - ¿enumdomusers devuelve usuarios?
    - → Sí: guardar lista completa con RIDs → revisar descripciones → pueden tener contraseñas
    - → No: continuar
    - **→ Ver nota: 445_135_SMB_RPC.md**
3. **LDAP anonymous bind**
    
    - ¿Devuelve usuarios o estructura del dominio?
    - → Sí: guardar usuarios → revisar campo description → frecuentemente tiene contraseñas hardcodeadas
    - → No: continuar
    - **→ Ver nota: 389_LDAP.md**
4. **Kerbrute con wordlist**
    
    - Validar usuarios vía Kerberos → más sigiloso que LDAP/RPC → menos logs generados
    - Wordlist recomendada: xato-net-10-million-usernames.txt → empezar con top-usernames-shortlist.txt
    - Resultado: lista de usuarios válidos confirmados → guardar en valid_users.txt
    - **→ Ver nota: 88_Kerberos.md**
5. **AS-REP Roasting** ← ejecutar siempre con la lista de usuarios obtenida
    
    - ¿Algún usuario no requiere preautenticación Kerberos?
    - → Sí: obtener hash krb5asrep → crackear offline con hashcat -m 18200
        - → Crackeaste: credenciales en claro → **ir a Fase 3**
        - → No crackeaste: guardar hash → continuar con spraying
    - → No: continuar
    - **→ Ver nota: 88_Kerberos.md**
6. **Password Spraying**
    
    - Verificar política de lockout ANTES → net accounts /domain o nxc smb --pass-pol
    - Lockout threshold = 0 → sin lockout → más agresivo
    - Lockout > 0 → máximo 1 contraseña por usuario por ronda → respetar siempre
    - Contraseñas a probar en orden: Password123, Welcome1, Estacion+año, NombreEmpresa+año
    - Via Kerberos → más sigiloso que via SMB → menos logs en el DC
    - → Éxito: credenciales en claro → **ir a Fase 3**
    - **→ Ver nota: 88_Kerberos.md / Hydra.md**
7. **DNS Zone Transfer**
    
    - ¿El DNS del DC permite zone transfer?
    - → Sí: lista completa de todos los hosts del dominio → más superficie de ataque
    - → No: continuar
    - **→ Ver nota: 53_DNS.md**
8. **SNMP si puerto 161 UDP está abierto**
    
    - Community string "public" → puede enumerar usuarios locales del sistema
    - **→ Ver nota: Nmap.md (sección SNMP)**

> **Si agotaste Fase 2 sin creds → revisar puerto 80/443 y otras apps expuestas → buscar vulnerabilidad de aplicación**

---

## FASE 3 — CON CREDENCIALES VÁLIDAS (user:pass)

> Ejecutar este bloque completo cada vez que obtengas credenciales nuevas

**Objetivo:** Entender qué puedes hacer con esas creds y encontrar el camino a DA

```
REGLA DE ORO → cada vez que obtienes credenciales nuevas:
  1. Validar en TODOS los servicios inmediatamente
  2. Correr BloodHound antes de atacar cualquier cosa
  3. No atacar a ciegas → BloodHound te dice el camino
```

1. **Validar en todos los servicios**
    
    - SMB → ¿admin local? ([Pwn3d!]) → dump SAM inmediato → más hashes
    - WinRM (5985) → ¿puedo conectarme? → shell interactiva → ir a Fase 4
    - LDAP → ¿tengo acceso? → dump completo del directorio
    - MSSQL → ¿está abierto? → xp_cmdshell → RCE → SeImpersonate → SYSTEM
    - RDP → ¿acceso gráfico? → sesión completa
    - ¿Hay DA logueado en algún host? → nxc smb subred --loggedon-users → objetivo prioritario
    - **→ Ver nota: Enumeracion_Usuarios_Windows_AD.md**
2. **BloodHound** ← ejecutar antes de cualquier ataque
    
    - Recopilar toda la información del dominio desde Kali con bloodhound-python
    - Queries clave en orden de prioridad:
        - Find Principals with DCSync Rights → ¿ya tengo DCSync? → ir a Fase 5
        - Shortest Path to Domain Admins → ¿cuántos saltos?
        - Shortest Path from Owned Principals → paths desde lo que ya tengo
        - Find AS-REP Roastable Users → más hashes sin creds adicionales
        - Find Kerberoastable Users → cuentas de servicio con SPNs
        - Node Info → Outbound Object Control → ACLs abusables desde mi usuario
    - **BloodHound te dice el camino → no ataques sin verlo primero**
    - **→ Ver nota: BloodHound.md**
3. **Kerberoasting** ← con cualquier cuenta de dominio
    
    - ¿Hay cuentas de servicio con SPNs?
    - → Sí: obtener hashes krb5tgs → crackear offline con hashcat -m 13100
        - Priorizar cuentas en grupos privilegiados → ver BloodHound
        - → Crackeaste: nueva cuenta comprometida → repetir Fase 3 con ella
    - → No: continuar
    - **→ Ver nota: 88_Kerberos.md**
4. **Enumerar más profundo**
    
    - Usuarios con descripciones → ldapdomaindump → domain_users.html → buscar contraseñas
    - Política de contraseñas del dominio → define agresividad del spraying siguiente
    - Usuarios con passwordNeverExpires → contraseñas muy antiguas → más fáciles de crackear
    - Cuentas con delegación sin restricción → alto valor → BloodHound las marca
    - Shares accesibles con estas creds → SYSVOL → GPP passwords → clave AES conocida
    - Sesiones activas en todos los hosts → nxc --loggedon-users → DA logueado en algún host?
    - **→ Ver nota: 389_LDAP.md / 445_135_SMB_RPC.md**

---

## FASE 4 — ESCALADA EN EL DOMINIO

> BloodHound ya te dijo el camino — aquí solo ejecutas lo que te indicó

```
Cada vez que comprometes una cuenta nueva → volver a Fase 3 con ella
Las rutas se encadenan → una cuenta lleva a otra → esa lleva a DA
```

**ACL Abuse** → BloodHound muestra el edge abusable

```
GenericAll sobre usuario      → cambiar su contraseña → nueva cuenta → repetir Fase 3
GenericAll sobre grupo        → añadirte al grupo → si es DA → Fase 5
ForceChangePassword           → cambiar contraseña sin conocer la actual → nueva cuenta
WriteDACL sobre dominio       → darte permisos DCSync → ir a Fase 5 directamente
GenericWrite sobre usuario    → agregar SPN → Kerberoast targeted → crackear
AddMember sobre grupo         → añadirte a grupo privilegiado → nuevos permisos
→ Después de cada abuso → verificar → volver a Fase 3 con la nueva cuenta
```

**→ Ver nota: ACL_Abuse_AD.md / 464_kpasswd.md**

**Kerberoasting** → crackeaste hash de cuenta privilegiada

```
→ Nueva cuenta comprometida → repetir Fase 3
→ Si la cuenta está en DA o grupo privilegiado → Fase 5
→ Si la cuenta tiene ACLs abusables → Fase 4 ACL Abuse
```

**→ Ver nota: 88_Kerberos.md**

**Admin local comprometido** → [Pwn3d!] en nxc

```
→ Dump de SAM + LSA Secrets → hashes locales + creds de servicios de dominio
→ Dump de cached credentials → hashes de usuarios de dominio que loguearon antes
→ Reutilizar hashes en otros hosts de la subred → nxc smb subred -H hash
→ Si hay DA logueado en el host → mimikatz → credenciales en claro → Fase 5
→ Si tengo SYSTEM → mimikatz sekurlsa::logonpasswords → todo lo que hay en memoria
```

**→ Ver nota: Mimikatz.md / 0_Metodologia_Windows_PrivEsc_Dominio.md**

**Lateral Movement** → tengo creds válidas en múltiples hosts

```
→ Moverse de host en host buscando DA logueado
→ Cada host nuevo → mimikatz → más credenciales → más superficie
→ Priorizar hosts donde BloodHound indica que hay DA logueado
→ Con PTH o PTT → moverse sin contraseña en claro
```

**→ Ver nota: Lateral_Movement.md**

**MSSQL** → si hay servidor SQL en la red

```
→ Impersonación → actuar como SA → xp_cmdshell → RCE
→ Linked servers → RCE en otro servidor SQL sin comprometerlo directamente
→ SeImpersonatePrivilege → Potato attack → SYSTEM
→ SYSTEM en servidor miembro → credenciales de dominio → Fase 4/5
```

**→ Ver nota: 1433_MSSQL.md**

---

## FASE 5 — DA COMPROMETIDO

```
Llegaste aquí porque:
  → Tienes DCSync rights → secretsdump → todos los hashes
  → Eres Domain Admin → evil-winrm al DC → mimikatz → todos los hashes
  → Admin local en el DC → SYSTEM → lsadump::sam → lsadump::dcsync
```

1. **DCSync → dump de todos los hashes del dominio**
    
    - Hash de Administrator → Pass-the-Hash → acceso a todos los hosts del dominio
    - Hash de krbtgt → Golden Ticket → persistencia indefinida → tickets válidos años
    - Hashes de otros DAs → redundancia si cambian la clave del admin
    - **→ Ver nota: DCSync.md**
2. **Pass-the-Hash con hash de Administrator**
    
    - nxc smb subred/24 → confirmar acceso a todos los hosts → [Pwn3d!] en todo
    - evil-winrm a cualquier host → shell como administrador
    - **→ Ver nota: Lateral_Movement.md**
3. **Persistencia opcional**
    
    - Golden Ticket con hash de krbtgt → 10 años de validez
    - Crear usuario backdoor en DA → acceso alternativo
    - DCSync periódico → mantenerse al día con cambios de contraseña
4. **Documentar y buscar flags**
    
    - Documentar la cadena completa de ataque para el reporte
    - Buscar flags: proof.txt en escritorio de Administrator en el DC
    - local.txt en escritorios de usuarios comprometidos en el camino

---

## FLUJO DE DECISIÓN RÁPIDO

```
¿Tienes creds?
│
├── NO → Fase 2
│         ├── Null session SMB/RPC/LDAP → usuarios + shares
│         ├── Kerbrute → usuarios válidos confirmados
│         ├── AS-REP Roasting → hash → crackear → ¿crackeaste? → SÍ → Fase 3
│         ├── Password Spraying → verificar lockout → una pass por ronda
│         └── DNS Zone Transfer + SNMP → más info
│                   → Sin creds después de todo esto → revisar apps web (80/443)
│
└── SÍ → Fase 3
          ├── Validar en todos los servicios → [Pwn3d!]? → dump SAM
          ├── BloodHound → ver el camino antes de atacar
          ├── Kerberoasting → más hashes → crackear → nueva cuenta → repetir
          ├── Enumerar descripiones → contraseñas hardcodeadas
          └── Seguir path de BloodHound → Fase 4
                    ├── ACL Abuse → nueva cuenta → repetir Fase 3
                    ├── Admin local → mimikatz → DA logueado? → Fase 5
                    ├── MSSQL → impersonación → RCE → SeImpersonate → creds dominio
                    ├── Lateral Movement → moverse hacia DA logueado
                    └── DCSync rights → secretsdump → Fase 5
```

---

## CHECKLIST PARA EL EXAMEN

```
[ ] Escaneo completo de puertos → identificar DC y servidores miembro
[ ] Identificar hostname y dominio de todos los hosts
[ ] Agregar todos los hosts a /etc/hosts
[ ] Sincronizar reloj con ntpdate
[ ] Activar Responder → dejar corriendo toda la sesión
[ ] Null session SMB → shares y usuarios
[ ] Null session RPC → enumdomusers → lista de usuarios
[ ] Anonymous LDAP → estructura del dominio + descripciones
[ ] Kerbrute → validar usuarios con wordlist
[ ] AS-REP Roasting → sin creds → con lista de usuarios
[ ] DNS Zone Transfer → lista completa de hosts
[ ] ¿Tengo creds? → validar en todos los servicios
[ ] BloodHound → antes de atacar cualquier cosa
[ ] Kerberoasting → con cualquier cuenta válida
[ ] LDAP dump → buscar contraseñas en descripciones
[ ] nxc --loggedon-users → ¿hay DA logueado en algún host?
[ ] Seguir path de BloodHound → no atacar a ciegas
```

---

## PUERTOS CLAVE DE UN DC

|Puerto|Servicio|Nota|
|---|---|---|
|53|DNS|Ver nota 53_DNS.md|
|88|Kerberos|Ver nota 88_Kerberos.md|
|135|RPC|Ver nota 445_135_SMB_RPC.md|
|139/445|SMB|Ver nota 445_135_SMB_RPC.md|
|389/636|LDAP/LDAPS|Ver nota 389_LDAP.md|
|464|kpasswd|Ver nota 464_kpasswd.md|
|3268/3269|Global Catalog|Ver nota 389_LDAP.md|
|5985/5986|WinRM|Ver nota 5985_5986_WinRM.md|
|1433|MSSQL|Ver nota 1433_MSSQL.md|
|3389|RDP|Ver nota 3389_RDP.md|