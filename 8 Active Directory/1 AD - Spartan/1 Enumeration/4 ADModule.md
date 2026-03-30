# AD Module DLL

Tags: #AD #ADModuleDLL #Powershell 

El "AD Module" es el módulo de Active Directory para Windows PowerShell, que ofrece cmdlets diseñados para administrar y consultar dominios de Active Directory (AD). Este conjunto de herramientas es invaluable tanto para administradores como para profesionales de seguridad, incluidos los hackers éticos. Sus funcionalidades están integradas en la biblioteca dinámica Microsoft.ActiveDirectory.Management.dll.

```bash 
1. Enumeración de objetos: Usando cmdlets como 'Get-ADUser', 'Get-ADGroup', y 'Get-ADComputer', un profesional de seguridad puede obtener una lista de usuarios, grupos y computadoras, respectivamente, dentro del dominio de AD. Estas listas son fundamentales para entender la estructura y el alcance del dominio.
    
2. Extracción de detalles específicos: Los cmdlets permiten una gran granularidad en la extracción de detalles. Por ejemplo, se puede usar 'Get-ADUser' para extraer detalles específicos sobre un usuario, como su dirección de correo electrónico, la última vez que inició sesión, si la cuenta está habilitada, etc.
    
3. Identificación de relaciones de confianza: Con cmdlets como 'Get-ADTrust', los profesionales de seguridad pueden identificar las relaciones de confianza entre diferentes dominios.
    
4. Auditoría de políticas de seguridad: Utilizando cmdlets como 'Get-ADDefaultDomainPasswordPolicy', un hacker ético puede obtener información sobre políticas de contraseña y otros ajustes de seguridad para determinar si cumplen con las mejores prácticas.
    
5. Búsqueda de configuraciones inseguras: A través de la enumeración y el análisis de la información recopilada, un profesional de seguridad puede identificar configuraciones que presentan riesgos, como cuentas con contraseñas que nunca caducan o usuarios que tienen privilegios excesivos.
```

## ADModule 

* [ADModule](https://github.com/samratashok/ADModule)

```powershell
❯ Import-Module .\Microsoft.ActiveDirectory.Management.dll -Verbose  # Importamos el modulo
```

```powershell
❯ Get-ADUser -Identity "unconstrained.user"   # Recupera información sobre un usuario específico o sobre todos los usuarios

❯ Get-ADComputer -Identity PC01 # Obtiene información sobre una máquina específica o sobre todas las máquinas en el dominio

❯ Get-ADGroup -Identity "Administrators"      # Recupera detalles de un grupo en Active Directory
   
❯ New-ADUser -Name "juan carlos"              # Crea un nuevo usuario en Active Directory

❯ Set-ADUser -Identity jdoe -Description "Account de prueba"     # Modifica propiedades de un usuario existente

❯ Remove-ADUser -Identity juan     # Elimina un usuario de Active Directory

❯ Get-ADDomain    # Recupera información sobre el dominio

❯ Get-ADForest    # Recupera información sobre el bosque de Active Directory

❯ Get-ADObject -Filter 'ObjectClass -eq "user"'    # Recupera un objeto de Active Directory basado en un conjunto de criterios

❯ Get-ADDomainController -Discover     # Encuentra controladores de dominio en el dominio especificado
```
