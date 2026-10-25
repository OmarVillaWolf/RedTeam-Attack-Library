# Windows Credential Harvesting

Tags: #Windows #MovimientoLateral 

## Página para PrivEsc
* [PrivEsc](https://www.hackingarticles.in/)

## Identificación 
```bash 
# Enumeración 

# Buscar software de terceros no estándar, antiguo o vulnerable.
# Ejemplos: servidores de correo, clientes de backup, herramientas administrativas, etc.
C:\Program Files
C:\Program Files (x86)

# Revisar completamente el perfil del usuario comprometido:
C:\Users\Omar\

# Buscar archivos de configuración, scripts, backups, credenciales, documentos o información sensible.
```


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

## Comandos básicos de reconocimiento 
```powershell 
❯ Get-Content file    # Mirar el contenido de un archivo sin ejecutarlo

# Reiniciar Windows inmediatamente
❯ shutdown /r /t 0 
```

```powershell 
# Hacer esto desde el usuario administrator
Paso 1:
# Poner comillas alrededor de los nombres de usuario
❯ net users /domain    # Crear una lista con los usuarios del dominio  
❯ paste -sd, userlist.txt | sed 's/[^,]*/"&"/g'  # En Kali 

Paso 2:
# Buscar recursivamente posibles credenciales en Windows
❯ Get-ChildItem -Path C:\ -Recurse -Force -Include *.config,*.ini,*.xml,*.bak,*.txt,*.ps1,*.log,*.json,*.yml,*.yaml,*.env,*.cs,*.vb,*.vbs,*.key,*.pem,*.crt,*.rdp,*.kdbx -File -ErrorAction SilentlyContinue |
Where-Object { $_.FullName -notmatch '(C:\\Windows)' } |
Select-String -Pattern "pwd=", "password=", "username=", "user=", "pass="


# Ejemplo 1:
❯ Get-ChildItem -Path C:\ -Recurse -Force -Include *.config,*.ini,*.xml,*.bak,*.txt,*.ps1,*.log,*.json,*.yml,*.yaml,*.env,*.cs,*.vb,*.vbs,*.key,*.pem,*.crt,*.rdp,*.kdbx -File -ErrorAction SilentlyContinue |
Where-Object { $_.FullName -notmatch '(C:\\Windows|C:\\Documents and Settings\\|C:\\Program Files\\VMware\\|C:\\Program Files\\Windows\\|C:\\Program Files\\Jenkins\\|C:\\ProgramData\\Application|Jenkins|C:\\ProgramData\\Microsoft|C:\\ProgramData\\VMware|C:\\Users\\Alex\\)' } |
Select-String -Pattern "pwd=", "password=", "username=", "user=", "pass=", "Administrator", "kpatel", "svc_legacy", "svc_update", "eturner", "svc_monitor", "svc_webapi", "svc_admin", "svc_reports" 

# Ejemplo 2:
❯ Get-ChildItem -Path C:\ -Recurse -Force -Include *.config,*.ini,*.xml,*.bak,*.txt,*.ps1,*.log,*.json,*.yml,*.yaml,*.env,*.cs,*.vb,*.vbs,*.key,*.pem,*.crt,*.rdp,*.kdbx -File -ErrorAction SilentlyContinue |
Where-Object { $_.FullName -notmatch '(C:\\Windows|C:\\Program Files\\WindowsApps|C:\\ProgramData\\Microsoft\\Windows|C:\\Users\\All Users\\Microsoft|C:\\Users\\Nick)' } |
Select-String -Pattern "pwd=", "password=", "username=", "user=", "pass=", "Administrator", "kpatel", "svc_legacy", "svc_update", "eturner", "svc_monitor", "svc_webapi", "svc_admin", "svc_reports"
```

```powershell 
❯ net user <user1>
❯ net localgroup Administrators 
```

## PowerShell History
```powershell
❯ Get-Content (Get-PSReadLineOption).HistorySavePath   <- IMPORTANTE 

# Leer el historial del usuario 
❯ cat $env:USERPROFILE\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt    <- IMPORTANTE 

❯ type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt

❯ type C:\Users\<usuario>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

## Buscar creds ocultas 
```powershell 
# Listar contenido oculto (Hacerlo en C:\)
❯ dir -Force    

	C:\PSTranscripts\20191203\
		PowerShell_transcript.RESOLUTE.OJuoBGhU.20191203063201.txt
		
	C:\'$Recycle.Bin'
	C:\$Recycle.Bin\S-1-5-18
```

## Filtrado a buscar credenciales en algún servicio/proceso
```powershell 
# Enumerar servicios/procesos desde el registro     <-- MEJOR OPCIÓN 
❯ Get-ChildItem HKLM:\SYSTEM\CurrentControlSet\Services | ForEach-Object {
    $ip = $_.GetValue("ImagePath")
    "$($_.PSChildName) = $ip"
}

NOTA:
	- A veces hay credenciales en esos procesos (Se debe filtrar)


# Enumerar procesos que se estan ejecutando
❯ Get-Process | Sort-Object CPU -Descending | Format-Table Name, Id, CPU, WorkingSet -AutoSize
```

```powershell 
❯ Get-ChildItem HKLM:\SYSTEM\CurrentControlSet\Services | ForEach-Object {
    $imagePath = $_.GetValue("ImagePath")
    $serviceName = $_.PSChildName
    
    if ($imagePath -match "-u\s+\S+|-p\s+\S+|/u\s+\S+|/p\s+\S+|--user|--password|username|password") {
        # Extrae solo la credencial
        $credMatch = [regex]::Matches($imagePath, '(?:-u|-p|/u|/p|--user|--password)\s+(\S+)')
        
        [PSCustomObject]@{
            "Servicio" = $serviceName
            "Parámetros_Sospechosos" = ($credMatch.Groups[1].Value -join ", ")
            "Ruta_Completa" = $imagePath
        }
    }
} | Format-Table -AutoSize
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
❯ Get-ChildItem Env:    # Mirar el contenido de todas las variables de entorno 
❯ $env:PATH
❯ $env:USERPROFILE

# CMC
❯ echo %PATH%
❯ echo %USERPROFILE%
```

## Historial RDP/SSH
```powershell
❯ reg query "HKCU\Software\Microsoft\Terminal Server Client" /s

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
