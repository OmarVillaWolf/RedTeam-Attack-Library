# Powershell

Tags: #Windows #Powershell #Comandos 

```bash 
Extensiones en Powershell:
- .ps1
- .psm1
- .psd1
```

## Variables 

```powershell 
$var = "Hola"                                 # Definir una variable 
Write-Host -ForegroundColor Yellow $var       # Imprimir la variable y colocarle un color
```

## Funciones 

```powershell
# Validación de las reglas en la maquina en donde se esta

function verificarFirewall(){
	$fw = New-Object -ComObject hnetcfg.fwpolicy2
	$reglas = $fw.rules | Where-Object {$_.Enabled -eq "True" -and $_.Direction -eq "1" -and $_.Name -like "*SMB*" } | Select-Object Name, LocalPorts, RemoteAddresses 
	return $reglas
}

verificarFirewall   # Llamar a la función
```

```powershell 
# Muestra los nombres de los servicios de la maquina 

function llamarServicio(){
	$servicio = Get-WmiObject win32_service | Format-Table name 
	return $servicio
}

llamarServicio      # Llamar a la función
```

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

## Enumeración

```powershell
❯ net user /domain                   # Mirar los usuarios del dominio 
❯ net user                           # Mirar los usuarios locales 
❯ net localgroup administrators      # Muestra los miebros del grupo local de administradores
```

```powershell 
❯ Find-WMILocalAdminAccess.ps1 -Verbose  # Enumeración de otras maquinas donde el usuario actual tiene acceso 
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