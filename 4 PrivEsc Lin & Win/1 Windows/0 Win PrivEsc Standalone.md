# Metodología — Windows Privilege Escalation (Standalone)

Tags: #PrivEsc #Windows #Metodologia #Escalada #SYSTEM #Local

## OBJETIVO
Mapa completo de enumeración y escalada de privilegios en máquinas Windows standalone (sin dominio o no unidas a AD).
Los comandos están en las notas individuales de cada técnica.

## TIPS 
1. **whoami /priv → lo primero siempre → SeImpersonatePrivilege = Potato attack directo**
2. **winPEAS → automatizar la enumeración → pero entender lo que hace**
3. **Los servicios mal configurados son el vector más común en el examen**
4. **Credenciales en el sistema → subestimado → revisar siempre antes de técnicas complejas**
5. **Siempre verificar con whoami después de explotar → confirmar escalada**
6. **PowerUp.ps1 → script de enumeración específico para PrivEsc Windows**

---

## ORDEN DE PRIORIDAD

```
 1. SeImpersonatePrivilege    → Potato attack → SYSTEM casi garantizado
 2. SeBackupPrivilege         → leer SAM/SYSTEM → dump de hashes locales
 3. SeDebugPrivilege          → inyectar en procesos → SYSTEM
 4. AlwaysInstallElevated     → instalar MSI como SYSTEM
 5. Unquoted Service Path     → plantar ejecutable en ruta con espacio
 6. Weak Service Permissions  → modificar binario o configuración del servicio
 7. Weak Registry Permissions → modificar servicios vía registro
 8. DLL Hijacking             → plantar DLL maliciosa en ruta buscada
 9. Scheduled Tasks           → tarea como SYSTEM con script escribible
10. Credenciales en sistema   → historial, registry, archivos de config
11. AlwaysInstallElevated     → registro habilitado → MSI malicioso
12. Kernel Exploit            → último recurso → puede crashear el sistema
```

---

## CHECKLIST RÁPIDO PARA EL EXAMEN

```
[ ] whoami /priv → SeImpersonatePrivilege, SeBackupPrivilege, SeDebugPrivilege
[ ] whoami /groups → grupos del usuario → Administrators, Backup Operators
[ ] systeminfo → versión OS y build → buscar Kernel CVEs
[ ] systeminfo | findstr Hotfix → parches instalados → parches que faltan
[ ] Subir winPEAS → ejecutar → revisar output completo
[ ] Subir PowerUp.ps1 → Invoke-AllChecks → resumen de vectores
[ ] sc qc → revisar servicios con rutas → Unquoted Service Path
[ ] Get-Acl en servicios → Weak Service Permissions
[ ] reg query AlwaysInstallElevated → si vale 1 → MSI malicioso
[ ] Buscar credenciales → historial PS, registry, archivos config, unattend.xml
[ ] icacls en ejecutables de servicios → permisos débiles
[ ] schtasks /query → tareas programadas como SYSTEM con script escribible
[ ] reg query SAM → si tienes SeBackupPrivilege → dump hashes
[ ] wmic service → servicios con rutas sin comillas
```

---

## CONTEXTO INICIAL — LO PRIMERO AL ENTRAR

```
❯ whoami → ¿quién soy?
❯ whoami /priv → ¿qué privilegios tengo?
❯ whoami /groups → ¿a qué grupos pertenezco?
❯ systeminfo → ¿qué OS y build es?
❯ hostname → ¿nombre de la máquina?
❯ net user → ¿qué usuarios hay?
❯ net localgroup administrators → ¿quién es admin?
❯ wmic useraccount get name,domain → Enumeración local 
```

**→ Ver nota: 2 Windows Commands.md**
**→ Ver nota: 3 PowerShell Commands.md**

---

## FASE 0.5 — ENUMERACIÓN DE USUARIOS

Antes de buscar vectores de escalada → enumerar usuarios disponibles
→ Ver nota: 2.1 Users Win y AD.md

Vectores útiles en standalone:
  - RPC null session → rpcclient → enumdomusers
  - SNMP → usuarios locales vía OID
  - WMI/PowerShell → net user → Get-LocalUser
  - MSSQL → si está expuesto → sys.server_principals

---

## FASE 1 — ENUMERACIÓN AUTOMATIZADA

**Objetivo:** Mapa rápido de todos los vectores potenciales

```
Subir y ejecutar winPEAS → revisar output por colores
  Rojo → crítico → revisar primero
  Amarillo → revisar después

Subir PowerUp.ps1 → Invoke-AllChecks
  Resumen directo de servicios vulnerables, AlwaysInstallElevated, etc.

No confiar ciegamente → complementar con enumeración manual
```

**→ Ver nota: 1 PowerUp.md**
**→ Ver nota: 2 winPEAS.md**

---

## FASE 2 — PRIVILEGIOS DE TOKEN (SeImpersonatePrivilege)

**Objetivo:** Aprovechar SeImpersonatePrivilege para escalar a SYSTEM

```
❯ Get-CimInstance Win32_OperatingSystem | select OSArchitecture

❯ whoami /priv → ¿SeImpersonatePrivilege habilitado?
  SÍ → Potato attack → SYSTEM casi garantizado

¿Qué versión de Windows es?
  Server 2008/2012/2016, Win 10 pre-1809  → JuicyPotato
  Server 2008/2012/2016,2019, win 10      → PetitPotato
  Server 2019, Win 10 post-1809           → PrintSpoofer o GodPotato
  Server 2022, Win 11                     → GodPotato o SweetPotato
  No sé la versión                        → GodPotato → funciona en casi todo
```

**Otros privilegios de interés:**
```
SeBackupPrivilege  → leer SAM y SYSTEM → dump de hashes locales → crackear
SeDebugPrivilege   → inyectar en lsass → dump de credenciales
SeRestorePrivilege → escribir en cualquier archivo → plantar DLL o binario
SeTakeOwnershipPrivilege → tomar propiedad de archivos → sobrescribir binarios
```

**→ Ver nota: 3 Token-Ptiv, Groups.md**

---

## FASE 3 — SERVICIOS MAL CONFIGURADOS

**Objetivo:** Aprovechar servicios del sistema con configuración débil

### 3.1 Unquoted Service Path
```
Condición: ruta del ejecutable del servicio tiene espacios y sin comillas
  C:\Program Files\My App\service.exe  → sin comillas → vulnerable
  → Windows busca: C:\Program.exe, C:\Program Files\My.exe, etc.
  → Plantar ejecutable malicioso en alguna de esas rutas
  → Cuando el servicio reinicie → ejecuta tu binario como SYSTEM

Herramientas: wmic service, sc qc, PowerUp.ps1
```

### 3.2 Weak Service Permissions
```
Condición: tu usuario tiene permisos de escritura sobre el ejecutable del servicio
  → Reemplazar el ejecutable con uno malicioso
  → Reiniciar el servicio → ejecuta tu binario

Condición alternativa: permisos para modificar la configuración del servicio
  → Cambiar el binPath del servicio a tu comando
  → sc config servicio binPath= "cmd /c tu_comando"
  → Reiniciar el servicio

Herramientas: accesschk.exe, PowerUp.ps1, Get-Acl
```

### 3.3 Weak Registry Permissions
```
Condición: tu usuario puede escribir en la clave de registro del servicio
  HKLM\SYSTEM\CurrentControlSet\Services\<servicio>
  → Modificar ImagePath → apuntar a tu ejecutable
  → Reiniciar el servicio

Herramientas: accesschk.exe, PowerUp.ps1
```


---

## FASE 4 — ALWAYSINSTALLELEVATED

**Objetivo:** Instalar un MSI malicioso con privilegios de SYSTEM

```
Condición: ambas claves del registro valen 1
  HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer → AlwaysInstallElevated = 1
  HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer → AlwaysInstallElevated = 1

Flujo:
  1. Verificar ambas claves con reg query
  2. Generar MSI malicioso con msfvenom → -f msi
  3. Ejecutar el MSI → instala como SYSTEM
  4. Reverse shell o usuario nuevo como SYSTEM
```


---

## FASE 5 — DLL HIJACKING

**Objetivo:** Plantar una DLL maliciosa que sea cargada por un proceso privilegiado

```
Condición: proceso privilegiado busca DLL en directorio donde puedo escribir
  → Proceso usa LoadLibrary sin ruta absoluta
  → Busca en: directorio del exe, directorios del PATH, System32, etc.
  → Si alguno es escribible → plantar DLL maliciosa con el nombre esperado
  → Cuando el proceso cargue la DLL → ejecuta tu código como SYSTEM

Herramientas: ProcMon (para identificar DLLs no encontradas), PowerUp.ps1
```


---

## FASE 6 — TAREAS PROGRAMADAS

**Objetivo:** Aprovechar tareas programadas que ejecutan scripts modificables

```
Condición: tarea programada corre como SYSTEM y ejecuta script escribible
  → Modificar el script → añadir reverse shell o crear usuario admin
  → Esperar a que la tarea se ejecute

Herramientas: schtasks /query, PowerUp.ps1, accesschk.exe
```


---

## FASE 7 — CREDENCIALES EN EL SISTEMA

**Objetivo:** Encontrar credenciales guardadas para reutilizarlas o escalar

```
Lugares a revisar:
  Historial de PowerShell     → credenciales usadas en comandos anteriores
  Credential Manager          → cmdkey /list → runas /savecred
  Registry Winlogon           → autologon → contraseña en claro
  Unattend.xml                → instalación desatendida → credenciales en base64
  web.config / applicationHost.config → credenciales de IIS y apps web
  Archivos .txt, .xml, .ini   → findstr recursivo buscando "password"
  SAM + SYSTEM                → con SeBackupPrivilege → dump de hashes locales

Credencial encontrada → probar en:
  → net use / net localgroup → añadir al grupo Administrators
  → evil-winrm, RDP, SMB con las creds
```


---

## FASE 8 — KERNEL EXPLOITS

**Objetivo:** Explotar vulnerabilidades del kernel — ÚLTIMO RECURSO

```
⚠️ Puede crashear el sistema → usar solo si no hay otra opción

Verificar versión → systeminfo → buscar CVEs:
  MS17-010  (EternalBlue)     → Windows 7, Server 2008 R2
  MS16-032                    → Windows 7-10, Server 2008-2012
  CVE-2021-1675 (PrintNightmare) → casi todos los Windows
  CVE-2021-34527 (PrintNightmare remoto) → Server 2019 y anteriores
  CVE-2022-21882              → Windows 10, Server 2019/2022
```


---

## FLUJO DE DECISIÓN RÁPIDO

```
Shell como usuario no privilegiado en Windows standalone
│
├── whoami /priv → SeImpersonatePrivilege?
│     └── SÍ → Potato attack → SYSTEM directo
│
├── winPEAS + PowerUp → revisar output
│     ├── Unquoted Service Path → plantar binario → reiniciar servicio
│     ├── Weak Service Permissions → modificar binPath → reiniciar
│     ├── AlwaysInstallElevated → MSI malicioso → SYSTEM
│     └── DLL Hijacking → plantar DLL → esperar proceso
│
├── Credenciales en el sistema
│     ├── cmdkey /list → runas /savecred → ejecutar como admin
│     ├── Winlogon → autologon → contraseña en claro
│     └── Unattend.xml → base64 → credenciales
│
├── Tareas programadas → script escribible → modificar → esperar
│
└── systeminfo → CVE conocido → Kernel Exploit → último recurso
```

---

## REFERENCIAS A NOTAS ESPECÍFICAS

```
Windows_Comandos.md           → Referencia de comandos Windows
PowerShell_Comandos.md        → Referencia de comandos PowerShell
winPEAS.md                    → Enumeración automatizada
PowerUp.md                    → Enumeración específica de PrivEsc
Token_Impersonation.md        → SeImpersonatePrivilege y otros tokens
Potato_Attacks.md             → JuicyPotato, PrintSpoofer, GodPotato
Unquoted_Service_Path.md      → Servicios con rutas sin comillas
Weak_Service_Permissions.md   → Permisos débiles en servicios
DLL_Hijacking.md              → Secuestro de DLLs
Scheduled_Tasks_Windows.md    → Tareas programadas
AlwaysInstallElevated.md      → Instalación MSI elevada
Password_Hunting_Windows.md   → Búsqueda de credenciales
Kernel_Exploits_Windows.md    → Exploits de kernel
```
