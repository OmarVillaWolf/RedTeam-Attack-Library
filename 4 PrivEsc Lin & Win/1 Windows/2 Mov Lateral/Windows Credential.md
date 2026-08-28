# Windows Credential Harvesting

Tags: #Windows #MovimientoLateral 

## Identificación 

```bash 
# Windows — aplicaciones web

C:\inetpub\wwwroot\<APP>\web.config
C:\inetpub\wwwroot\<APP>\config.php
C:\inetpub\wwwroot\<APP>\db.php
C:\inetpub\wwwroot\<APP>\.env

C:\xampp\htdocs\<APP>\config.php
C:\xampp\htdocs\<APP>\db.php
C:\xampp\htdocs\<APP>\.env

C:\wamp64\www\<APP>\config.php
C:\wamp64\www\<APP>\db.php
```
## PowerShell History

```powershell
❯ Get-Content (Get-PSReadLineOption).HistorySavePath
❯ type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
❯ type C:\Users\<usuario>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

## Credenciales Almacenadas (Credential Manager)

```powershell
❯ cmdkey /list
❯ ls %APPDATA%\Microsoft\Credentials
❯ ls %APPDATA%\Microsoft\Protect
```

## Búsqueda en Registro

```powershell
❯ reg query HKLM /f password /t REG_SZ /s
❯ reg query HKCU /f password /t REG_SZ /s
❯ reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

## Variables de Entorno

```powershell
❯ Get-ChildItem Env:
❯ echo %PATH%
❯ echo %USERPROFILE%
```

## Historial RDP/SSH

```powershell
❯ reg query "HKCU\Software\Microsoft\Terminal Server Client\Default"
❯ Get-ChildItem C:\Users\<usuario>\.ssh\
❯ Get-ChildItem C:\Users\<usuario>\.ssh\id_rsa
```

## Git Credentials

```powershell
❯ type C:\Users\<usuario>\.git-credentials
❯ type C:\Users\<usuario>\.gitconfig
```

## Búsqueda de Archivos con Credenciales

```powershell
❯ Get-ChildItem -Path C:\Users\<usuario> -Recurse -ErrorAction SilentlyContinue | Select-String "password|credential|secret|pass" -List
```

## Scheduled Tasks

```powershell
❯ Get-ScheduledTask -TaskPath * | Get-ScheduledTaskInfo
❯ schtasks /query /v /fo list
```

## Archivos Recientes (Desktop/Documents)

```powershell
❯ ls C:\Users\<usuario>\Desktop\
❯ ls C:\Users\<usuario>\Documents\
❯ ls C:\Users\<usuario>\Downloads\
```

## RunMRU (Comandos ejecutados recientemente)

```powershell
❯ reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU"
```

## Services corriendo como usuario específico

```powershell
❯ Get-WmiObject Win32_Service | Where-Object {$_.StartName -ne "LocalSystem"} | select Name, StartName
```

## Browser Passwords

```powershell
❯ ls "$env:LOCALAPPDATA\Google\Chrome\User Data\Default\"
❯ ls "$env:APPDATA\Mozilla\Firefox\Profiles\"
```

## Archivos de Configuración Sensibles

```powershell
❯ Get-ChildItem -Path C:\Users\<usuario> -Recurse -Include *.config, *.xml, *.ini -ErrorAction SilentlyContinue
```

## DPAPI Decryption (si tienes acceso)

```powershell
# Extraer DPAPI credentials
❯ dir C:\Users\<usuario>\AppData\Roaming\Microsoft\Credentials\
# Luego usar herramientas como Mimikatz o LaZagne
```

## Recycle Bin

```powershell
❯ ls C:\$Recycle.Bin\
```

## Recent Files Registry

```powershell
❯ reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Recent Document Shortcuts"
```

## Event Logs (si tienes permisos)

```powershell
❯ Get-EventLog -LogName Security -InstanceId 4688 | select ProcessName, CommandLine
```
