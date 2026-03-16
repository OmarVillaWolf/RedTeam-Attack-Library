# Powershell

Tags: #Windows #Powershell #Comandos 

## Comandos 

```powershell 
❯ Get-Host      # Mirar la versión de PS
```

```powershell
❯ Test-Connection "192.168.0.1"      # Hacer un ping 
```

```powershell
# Obtener las llaves de registro 
❯ Get-Item -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\windows\CurrentVersion | Select-Object -ExpandProperty Property 
```

```powershell 
❯ cmd /c dir /r /s file.txt      # Buscar de manera recursiva un archivo 
❯ dir -Force                     # Buscar un archivo 
```

## Enumeración

```powershell
❯ net user /domain                   # Mirar los usuarios del dominio 
❯ net user                           # Mirar los usuarios locales 
❯ net localgroup administrators      # Muestra los miebros del grupo local de administradores
```

```powershell 
❯ Find-WMILocalAdminAccess.ps1 -Verbose  # Enumeración de otras máquinas donde el usuario actual tiene acceso 
```

```powershell
# Ir a un recurso, enumerar de manera recursiva los archivos, los guarda en la variable 'Files' y filtra lor archivos 

$Files = Get-ChildItem 'C:\Program Files\Files 2025\*.*' -Recurse
Get-Childitem $Files -Include *.json, *.txt, *.config, *.inc, *.prop, *.xml, *.sql -Recurse | Select-String -Pattern "password", "pwd", "user", "usr", "USER", "User", "API", "API_KEY", "KEY"
```

## Creación de una sesión a un servidor 

```powershell
❯ $session = New-PSSession -ComputerName 'server_name' -verbose  # Crear una variable para una nueva sesion
❯ $session    # Mirar el ID, Name, ComputerName
❯ Invoke-Command -Session $session -ScriptBlock {whoami;ipconfig} -verbose 
❯ Enter-PSSession -Session $session -verbose         # Ingresar a la session del servidor con una consola en PowerShell 

	❯ klist      # Mirar los ticktes 
```