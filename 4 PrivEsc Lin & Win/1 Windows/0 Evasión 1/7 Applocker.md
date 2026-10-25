# Applocker 

Tags: #Windows #MovimientoLateral #Evasion #AppLocker 

AppLocker es una funcionalidad de seguridad de Windows que permite a los administradores controlar qué aplicaciones y archivos pueden ejecutarse en un servidor o equipo Windows.

```powershell 
# Ruta de Powershell 
	C:\Windows\System32\WindowsPowerShell\v1.0

# Ruta por defecto en whitelist
	C:\Windows\System32\spool\drivers\color\
```

## Evadir la ejecución de Powershell 
```powershell 
# Copia PowerShell o tu ejecutable al directorio whitelisted
❯ copy C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe C:\Windows\System32\spool\drivers\color\powershell.exe

# Ejecutar desde ahí
❯ C:\Windows\System32\spool\drivers\color\powershell.exe
```

```powershell 
# Leer el historial del usuario 
❯ cat $env:USERPROFILE\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```
