# Bosques y relaciones de confianza

Tags: #AD #Windows #Persistencia #BosquesRelacionesConfianza

## Tipos de Relaciones de Confianza en Active Directory

```bash 
- Parent/Child: La relación de confianza entre dominios padre e hijo facilita la administración de seguridad y la cooperación entre ellos. Cualquier cambio en la política de seguridad en el dominio padre puede propagarse automáticamente al dominio hijo.
- Cross-link o shortcut trust: Específicamente, los enlaces cruzados pueden configurarse manualmente por los administradores de AD para optimizar las rutas de autenticación y autorización entre dominios que colaboran con frecuencia.
- External: Estas confianzas son especialmente útiles cuando se trabaja con dominios que están fuera de la estructura del bosque y son manejados por diferentes entidades administrativas.
- Tree-root: Estas confianzas son una extensión del modelo de confianza del bosque que permite la inclusión de nuevos dominios con distintos espacios de nombres, manteniendo la coherencia de la estructura de confianza global del bosque.
- Forest: Estas confianzas permiten la colaboración y la federación de identidades entre distintos bosques de Active Directory, a menudo entre organizaciones distintas que necesitan compartir recursos de manera segura.
- MIT: Se refiere a una interoperabilidad con otros sistemas de identidad que usan el estándar Kerberos y no son parte de un entorno de Windows. Esto es fundamental para la integración con sistemas heterogéneos en un entorno corporativo.
```

## Tipos de Confianza en AD - (TrustType)

```bash 
- 'DOWNLEVEL (No Active Directory) - 0x00000001:'
    - Cuando se menciona un dominio "DOWNLEVEL", estamos hablando de un dominio de Windows que existe en una red donde se utiliza la seguridad y administración de Windows, pero que no utiliza las características extendidas de Active Directory.
    - Estos dominios pueden estar corriendo versiones más antiguas de Windows, como Windows NT, que preceden a la implementación de Active Directory con Windows 2000.
    - En PowerView y otras herramientas de enumeración, estos dominios se etiquetan como `WINDOWS_NON_ACTIVE_DIRECTORY` para clarificar que, aunque operan bajo principios de Windows, no tienen las funcionalidades de un dominio de Active Directory.
        
- 'UPLEVEL (Con Active Directory) - 0x00000002:'
    - Los dominios "UPLEVEL" se refieren a aquellos que están corriendo una versión de Windows Server con Active Directory implementado.
    - Active Directory añade una capa robusta de servicios para la administración de usuarios, computadoras, políticas de seguridad, y otros recursos dentro de un entorno de red empresarial.
    - En herramientas como PowerView, estos se identifican como `WINDOWS_ACTIVE_DIRECTORY` para señalar que tienen todas las capacidades y características asociadas con Active Directory.
        
- 'MIT (Sistemas no-Windows con Kerberos) - 0x00000003:'
    - El tipo "MIT" identifica dominios de confianza que están ejecutando una versión de Kerberos que no es nativa de los sistemas operativos Windows, típicamente sistemas Unix o Linux que son conformes con el estándar RFC4120.
    - El nombre "MIT" viene de la institución que publicó el estándar RFC4120, el Instituto Tecnológico de Massachusetts (Massachusetts Institute of Technology, MIT), que desarrolló la implementación original de Kerberos.
    - Este tipo de confianza permite la interoperabilidad entre sistemas Windows y sistemas Unix/Linux para la autenticación y autorización de usuarios, extendiendo la administración de identidades y control de acceso más allá de los límites de un solo sistema operativo.
```

## Atributos de Confianza en AD - (**TrustAttributes**):

```bash
Los atributos de confianza (TrustAttributes) en Active Directory definen propiedades específicas y el comportamiento de las relaciones de confianza entre dominios o bosques.

1. 'NON_TRANSITIVE (0x00000001)' — La confianza no es transitiva. Esto significa que si el Dominio A confía en el Dominio B y el Dominio B confía en el Dominio C, el Dominio A no confía automáticamente en el Dominio C. Además, si una confianza es no transitiva, no podrás consultar información de Active Directory de dominios que estén más arriba en la cadena de confianza partiendo desde el punto no transitivo. Las confianzas externas son implícitamente no transitivas.
    
2. 'UPLEVEL_ONLY (0x00000002)' — Solo los clientes que operan con el sistema operativo Windows 2000 o versiones más recientes pueden utilizar la confianza. Esto se relaciona con versiones que soportan Active Directory y sus funcionalidades.
    
3. 'QUARANTINED_DOMAIN (0x00000004)' — El filtrado de SID (Identificador de Seguridad) está habilitado. Esto se refiere a una medida de seguridad que previene ciertos SIDs de un dominio confiado de ejercer derechos en el dominio de confianza. En PowerView, esto se muestra como FILTER_SIDS para simplificar.
    
4. 'FOREST_TRANSITIVE (0x00000008)' — Confianza transitiva entre el root de dos bosques de dominio que operan al menos con un nivel funcional de dominio de 2003 o superior. Esto permite que las confianzas se extiendan más allá de dos dominios directamente conectados a todos los dominios en los respectivos bosques.
    
5. 'CROSS_ORGANIZATION (0x00000010)' — La confianza es con un dominio o bosque que no es parte de la misma organización, lo que añade el SID de OTRA_ORGANIZACIÓN. Este atributo puede ser un poco confuso y no es comúnmente encontrado en entornos de producción. Según algunas fuentes, indica que la protección de autenticación selectiva está habilitada. Para más información, se puede consultar la documentación de MSDN relevante.
    
6. 'WITHIN_FOREST (0x00000020)' — El dominio de confianza se encuentra dentro del mismo bosque, lo que significa una relación padre-hijo o de enlace cruzado entre dominios.
```
## Enumeración utilizando nltest

```powershell
❯ nltest /domain_trusts /v 
```

```bash 
1. DOMAIN2 domain2.corp (NT 5)
    - (Direct Outbound): Esto sugiere que hay una relación de confianza en la que DOMAIN2 es la fuente de confianza, permitiendo a los usuarios de este dominio acceder a recursos en el dominio de destino.
    - (Direct Inbound): También hay una relación de confianza directa entrante, donde DOMAIN2 permite el acceso a sus recursos por parte de usuarios de otro dominio.
    - (Attr: quarantined): El dominio está marcado como en cuarentena, lo cual generalmente indica que el filtrado de SID está habilitado. Esto es una medida de seguridad que restringe qué SIDs (Identificadores de Seguridad) de otro dominio pueden ser reconocidos y aceptados. Esto es especialmente relevante para mitigar ciertos tipos de ataques de elevación de privilegios en los cuales un atacante podría intentar explotar las relaciones de confianza para acceder a recursos en un dominio que de otra manera estarían restringidos.
    - (NT 5): Se refiere a la versión del protocolo de autenticación, que en este caso indica que es compatible con Windows NT y versiones de Windows 2000 antes de que se introdujeran niveles funcionales más altos.
        
    
2. DOMAIN1 domain1.corp (NT 5)
    - (Forest Tree Root): DOMAIN1 es identificado como el dominio raíz de un bosque de dominio de Active Directory, que es el primer dominio creado en un nuevo bosque y es el contenedor principal para una estructura jerárquica de dominios.
    - (Primary Domain): Indica que este dominio es un dominio principal dentro del bosque. Esto no es típico de ver en una relación de confianza, ya que esta descripción es más comúnmente asociada con una estación de trabajo o un servidor que se une a un dominio, donde identifica al dominio al que está directamente asociado.
    - (Native): Significa que el dominio está operando en un nivel funcional nativo de Windows 2000 o superior, lo que permite todas las características de Active Directory disponibles para esa versión, sin restricciones de compatibilidad hacia atrás para los controladores de dominio más antiguos.
```

## Enumeración utilizando .NET Framework

```powershell
❯ ([System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()
```

## Enumeración utilizando con PowerView

```powershell
❯ Import-Module .\PowerView-3.0.ps1
❯ Get-DomainTrust
```

## Enumerando con SharpHound

```powershell
❯ .\SharpHound.exe -c All -d domain2.corp   
```