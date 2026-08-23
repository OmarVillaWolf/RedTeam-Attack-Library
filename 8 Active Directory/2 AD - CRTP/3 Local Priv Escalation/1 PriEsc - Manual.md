# Local Privilege Escalation (Enumeración)

Tags: #AD #Windows #Powershell #PrivEsc 

```bash 
Hay diferentes maneras de escalar privilegios localmente en Windows:

1. Parches faltantes 
2. Despliegue automatico y 'AutoLogon' password en texto claro 
3. AlwaysInstallElevated (Cualquier usuario puede correr MSI como SYSTEM)
4. Servicio mal configurados 
5. DLL Hijacking y mas 
6. Kerberos y NTLM relaying 
```

## Comandos Windows 

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
# Los servicios tienen un 'DACL' (Discretionary Access Control List)
❯ sc.exe sdshow snmptrap   
# Mostrar qué permisos tiene cada usuario/grupo sobre ese servicio 'snmptrap'


Output del comando:
D:(A;;CCLCSWRPWPDTLOCRRC;;;SY)
 (A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)
 (A;;CCLCSWLOCRRC;;;IU)
 (A;;CCLCSWLOCRRC;;;SU)
 (A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;WD)


Esto significa:
	- (A;;PERMISOS;;;IDENTIDAD) 
	  donde:
		A = Allow
		
		Permisos = Van de 2 en dos 
		DC → Change Config
		WD → Write DAC
		WO → Write Owner
		RP → Start
		WP → Stop
		
		Identidad (grupo/usuario) = 
		SY → LocalSystem
		BA → Built-in Administrators
		IU → Interactive Users
		SU → Service logon user
		WD → Everyone
```



