# Inicio de procesos con credenciales 

Tags: #Windows #MovimientoLateral 

## Enumeración 
```powershell 
# Enumerar procesos que se estan ejecutando
❯ Get-Process | Sort-Object CPU -Descending | Format-Table Name, Id, CPU, WorkingSet -AutoSize

# Enumerar servicios/procesos desde el registro
❯ Get-ChildItem HKLM:\SYSTEM\CurrentControlSet\Services | ForEach-Object {
    $ip = $_.GetValue("ImagePath")
    "$($_.PSChildName) = $ip"
}

NOTA:
	- A veces hay credenciales en esos procesos (Se debe filtrar)
```

## Filtrado a buscar credenciales en algún servicio/proceso
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