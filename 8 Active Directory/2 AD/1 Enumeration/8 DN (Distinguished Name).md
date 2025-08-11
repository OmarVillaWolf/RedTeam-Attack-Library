# Distinguished Name

Tags: #AD #DN #Powershell 

El término `'CN=Users,DC=Domain,DC=corp'` es un nombre distinguido (Distinguished Name o DN) en el contexto de Active Directory (AD) de Microsoft. Este tipo de notación se utiliza para identificar de forma única a los objetos dentro de un directorio de AD. A continuación, te explico en detalle cada componente de este nombre distinguido:

## Análisis Detallado

```bash 
1. CN=Users
    - 'CN' significa Common Name (Nombre Común).
    - 'Users' es el nombre común del objeto. En este caso, se refiere al contenedor o unidad organizativa predeterminada donde se almacenan los objetos de usuario en un dominio de AD. Este contenedor 'Users' es un lugar común para encontrar cuentas de usuario predeterminadas y creadas por el usuario.
        
2. DC=Domain,DC=corp
    - 'DC' significa Domain Component (Componente del Dominio).
    - 'Domain.corp' representa el nombre del dominio en el que se encuentra el objeto 'Users'. Este nombre de dominio está dividido en dos partes:
        - 'Domain': Sería el primer nivel o nombre distintivo del dominio.
        - 'corp': Es el sufijo del dominio, que suele indicar el tipo de organización o la naturaleza comercial del dominio (`corp` para corporativo, `com` para comercial, etc.). 
```

## Uso y Significado en Active Directory

```bash 
1. Identificación Única: En AD, cada objeto debe tener un DN único. Este DN proporciona una ruta clara para ubicar y gestionar el objeto dentro de la estructura jerárquica del directorio.
    
2. Administración de Usuarios: El contenedor 'CN=Users' es significativo porque es donde generalmente se almacenan y gestionan las cuentas de usuario en un dominio. La gestión de estos objetos incluye operaciones como la creación, modificación, y eliminación de cuentas de usuario, así como la asignación de políticas y permisos.
    
3. Búsqueda y Consulta LDAP: En operaciones de consulta LDAP (Lightweight Directory Access Protocol), los DNs son esenciales para localizar y manipular objetos dentro del directorio. Por ejemplo, al realizar búsquedas o aplicar configuraciones a través de scripts o herramientas de administración.
    
4. Importancia en la Seguridad: Para un profesional de la ciberseguridad o un pentester, entender la estructura de DN es vital para la exploración y evaluación de la seguridad de un dominio de AD. Por ejemplo, al buscar cuentas de usuario con configuraciones inseguras o permisos excesivos.
```