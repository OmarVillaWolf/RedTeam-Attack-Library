# Enumeración de Políticas de Grupo 

Tags: #AD #Powershell #GPO 


```powershell 
Las politicas de grupo proveen la habilidad para manejar configuraciones y cambios facilmente y cenrados en AD.
Una configuración de politica dolo puede afectar a un usuario o computadora 
	- Para computadoras: Configuración de seguridad, scripts de inicio y apagado, aplicaciones asignadas, etc..
	- Para usuarios: Configuración de seguridad, scripts de logon y logoff, aplicaciones asignadas y más 

Las politicas de grup[o estan contenidas en un 'Group Policy Object - GPO'
1. GPO: Es una colección virtual de configuraciones de politica, permisos de seguridad y 'scope of management - SOM' que se puede aplicar a usuarios y computadoras 
2. GPO pueden ser enlazadas a los dominios, sitios y unidades organizacionales 'OUs'
3. GPOs demasiado permisivos pueden ser abusados por varios ataques como 'privesc, backdoors, persistence, etc...'

Las organizaciones usan unidades organizacionales 'OUs' para delegar la administración 
1. OU es el nivel mas bajo del contenedor del AD para el cual un GPO puede ser aplicado 
2. Los ataques a los niveles de OU son debidos a la mal configuración o GPOs demasiado permisivos son más comunes en entornos empresariales 
```

## PowerView 

```powershell 
❯ Get-DomainGPO               # Obtener una lista de los GPO en el dominio actual 
❯ Get-DomainGPO | select displayname 
❯ Get-DomainGPO -ComputerIdentity dcorp-user1        

❯ Get-DomainGPOLocalGroup     # Obtener GPO(s) el cual usa grupos restringidos o 'groups.xml' para usuarios 

❯ Get-DomainGPOComputerLocalGroupMapping -ComputerIdentity dcorp-user1   # Obtener usuarios los cuales estan en un grupo local de una maquina usando GPO
	
❯ Get-DomainGPOUserLocalGroupMapping -Identity student1 -Verbose       # Obtener maquinas donde el usuario dado es miembro de un grupo especifico 

❯ Get-DomainOU     # Obtener OUs en un dominio 
❯ Get-DomainOU | select name 
❯ Get-DomainOU | select -ExpandProperty name 
❯ (Get-DomainOU -Identity DevOps).distinguishedname | %{Get-DomainComputer -SearchBase &_} | select name 
❯ (Get-DomainOU -Identity DevOps).gplink    # Obtener el GPOname desde el atributo gplink 

❯ Get-ADOrganizationalUnit -Filter * -Properties *      # Usar el powershell module 

❯ Get-DomainGPO -Identity "{0D1CC23D-1F20-4EEE-AF64-D99597AE2A6E}"      # Obtener GPO aplicado a un OU. Leer GPOname desde el atributo 'gplink' desde Get-NetOU 
	# Identity = Colocar el 'CN'
```