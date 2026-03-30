# Enumeración de Políticas de Grupo 

Tags: #AD #Powershell #GPO 

```powershell 
Las politicas de grupo proveen la habilidad para manejar configuraciones y cambios fácilmente y centrados en AD.
Una configuración de política dolo puede afectar a un usuario o computadora 
	- Para computadoras: Configuración de seguridad, scripts de inicio y apagado, aplicaciones asignadas, etc..
	- Para usuarios: Configuración de seguridad, scripts de logon y logoff, aplicaciones asignadas y más 

Las políticas de grupo estan contenidas en un 'Group Policy Object - GPO'
1. GPO: Es una colección virtual de configuraciones de política, permisos de seguridad y 'scope of management - SOM' que se puede aplicar a usuarios y computadoras 
2. GPO pueden ser enlazadas a los dominios, sitios y unidades organizacionales 'OUs'
3. GPOs demasiado permisivos pueden ser abusados por varios ataques como 'privesc, backdoors, persistence, etc...'

Las organizaciones usan unidades organizacionales 'OUs' para delegar la administración 
1. OU es el nivel mas bajo del contenedor del AD para el cual un GPO puede ser aplicado 
2. Los ataques a los niveles de OU son debidos a la mal configuración o GPOs demasiado permisivos son más comunes en entornos empresariales. 
```

## Enumeración de GPOS con PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```powershell 
❯ . C:\AD\PowerView.ps1               # Cargar PowerView en memoria 
❯ Import-Module .\PowerView.ps1       # Importar el módulo 
```
```powershell 
❯ Get-DomainGPO               # Obtener una lista de los GPO en el dominio actual 
❯ Get-DomainGPO | select displayname 
❯ Get-DomainGPO -ComputerIdentity dcorp-user1        

❯ Get-DomainGPOLocalGroup     # Obtener GPO(s) el cual usa grupos restringidos o 'groups.xml' para usuarios 

# Obtener usuarios los cuales estan en un grupo local de una máquina usando GPO
❯ Get-DomainGPOComputerLocalGroupMapping -ComputerIdentity dcorp-user1   

# Obtener máquinas donde el usuario dado es miembro de un grupo específico 
❯ Get-DomainGPOUserLocalGroupMapping -Identity student1 -Verbose       
```

```powershell 
❯ Get-DomainOU     # Obtener OUs en un dominio 
❯ Get-DomainOU | select name 
❯ Get-DomainOU | select -ExpandProperty name    # Listar todos los OUs
❯ (Get-DomainOU -Identity 'OU').distinguishedname | %{Get-DomainComputer -SearchBase $_} | select name          # Listar todas las computadoras en un OU en específico 

❯ (Get-DomainOU -Identity DevOps).gplink    # Obtener el GPOname desde el atributo gplink 
❯ Get-DomainGPO -Identity '{0D1CC23D-1F20-4EEE-AF64-D99597AE2A6E}'     # Obtener GPO aplicado a un OU. Leer GPOname desde el atributo 'gplink' desde Get-NetOU 
	# Identity = Colocar el 'cn'

```

```powershell 
❯ Get-ADOrganizationalUnit -Filter * -Properties *      # Usar el módulo de powershell
```