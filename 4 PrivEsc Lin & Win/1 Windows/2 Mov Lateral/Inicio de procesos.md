# Inicio de procesos con credenciales 

Tags: #Windows #MovimientoLateral 

```powershell 
# Enumerar procesos que se estan ejecutando
❯ Get-Process | Sort-Object CPU -Descending | Format-Table Name, Id, CPU, WorkingSet -AutoSize

# Enumerar servicios desde el registro
❯ Get-ChildItem HKLM:\SYSTEM\CurrentControlSet\Services | ForEach-Object {
    $ip = $_.GetValue("ImagePath")
    "$($_.PSChildName) = $ip"
}


NOTA:
	- A veces hay credenciaales en esos procesos (Se debe filtrar)
```