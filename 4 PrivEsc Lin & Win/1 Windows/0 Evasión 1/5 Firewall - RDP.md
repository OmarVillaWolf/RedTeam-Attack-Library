# Firewall

Tags: #Firewall 

## Habilitar RDP en el Registro
```powershell
# Establecer 'fDenyTSConnections = 0', habilitando RDP en esa configuración. Esto no garantiza por sí solo que puedas conectarte; también deben cumplirse otros requisitos del servicio, firewall y permisos

Paso 1:
❯ reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f

Paso 2: 
# Abrir el puerto 3389 en el firewall
❯ netsh firewall add portopening TCP 3389 "Remote Desktop"
```