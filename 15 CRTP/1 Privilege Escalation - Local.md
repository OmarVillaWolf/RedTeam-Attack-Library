
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
> Get-WmiObject -Class win32_service | select pathname 
> sc.exe sdshow snmptrap   
```

## PowerUP

* [PowerUP]()

```powershell 
Problemas de servicios 

> Get-ServiceUnquoted -Verbose   # Obtener servicios con rutas entre comillas y un espacio en sus nombres 
> Get-ModifiableServiceFile -Verbose    # Obtener servicios donde el usuario actual puede escribir en su ruta binaria o cambiar los argumentos del binario 
> Get-ModifiableService -Verbose    # Obtener los servicios cuya configuración puede modificar el usuario actual 

> Invoke-Allchecks
```

## Privesc 

* [Privesc]()

```powershell 
> Invoke-PrivEscCheck 
```

## WinPEAS 

* [WinPEAS]()

```powershell 
> winPEASx64.exe    
```
