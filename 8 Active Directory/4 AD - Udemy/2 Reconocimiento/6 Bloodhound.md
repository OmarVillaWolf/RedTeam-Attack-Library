# Bloodhound 

Tags: #AD #Bloodhound #Kali #SharpHound 

* [Custom-Queries-Bloodhound-Github](https://github.com/hausec/Bloodhound-Custom-Queries/blob/master/customqueries.json)
* [Custom-Queries-Bloodhound-Github](https://github.com/CompassSecurity/BloodHoundQueries/blob/master/customqueries.json)

```bash 
Se puede usar la Query en la parte de 'Raw Query' en bloodhound o se puede importar todo el Json
```

## Recopilación de datos 

* [Shaphound-github](https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.ps1)

```bash 
1. Copiar y pegar el código en la máquina Windows dentro de un txt y convertirlo a .ps1 
```

```bash 
❯ . .\sharphound.ps1     # Importar el script 
❯ Invoke-BloodHound -CollectionMethod All   # Recopilación de los datos en la máquina Windows donde creará dos archivos para compartirlos a Kali y hacer el análisis con Bloodhound 
```

## Análisis

```bash 
# Busqueda por 'Node info'  
	- user@corp.local           # Buscar por usuario
	- domain users@corp.local   # Buscar por grupo 

# Busqueda por 'Analysis'
	- Find Shortest Paths to Domain Admins (Empezar)
	- Find Principals with DCSync Rights 
	- Find All Domian Admins
	- Shortest Paths from Domain Users to High Values Targets 
	- Shortest Paths to Domain Admins from Owned Principals 

# En los grupos se puede buscar:
	- Inbound Control Rights   (Quien tiene permisos sobre ti)
		- Explicit Object Controllers 

# En los usuarios se puede buscar:
	- Outbound Control Right   (Sobre quien tienes permisos)
		- First Degree Object Control 
```