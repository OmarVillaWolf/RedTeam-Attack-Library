# Enumeración Manual 

Tags: #AD #Enumeracion #Powershell 

```bash 
Active Directory (AD) es una base de datos y un conjunto de servicios que conectan usuarios con los recursos de red que necesitan para hacer su trabajo. Aquí están algunas de las funcionalidades más importantes del Active Directory y cómo acceder a ellas a través del comando "Ejecutar" en Windows 'Win + R':

1. Usuarios y Equipos de Active Directory (ADUC) - 'dsa.msc'
    - Acceso: Escribe 'dsa.msc' en el comando "Ejecutar".
    - Función: Permite administrar cuentas de usuarios, grupos, y equipos. Puedes crear y administrar objetos de usuario, resetear contraseñas, y organizar estos objetos en unidades organizativas (OUs).
        
2. Política de Grupo (GPMC) - 'gpmc.msc'
    - Acceso: Escribe 'gpmc.msc' en el comando "Ejecutar".
    - Función: Administra y configura las políticas de grupo (GPOs) para usuarios y computadoras dentro de un dominio. Permite centralizar la gestión de configuraciones de seguridad y comportamientos de usuario/equipo.
        
3. Sitios y Servicios de Active Directory - 'dssite.msc'
    - Acceso: Escribe 'dssite.msc' en el comando "Ejecutar".
    - Función: Administra los sitios de AD y los servicios asociados a ellos, incluyendo la replicación de AD y la topología de la red.
        
4. Dominios y Confianzas de Active Directory - 'domain.msc'
    - Acceso: Escribe 'domain.msc' en el comando "Ejecutar".
    - Función: Gestiona los dominios de AD y las relaciones de confianza entre dominios y bosques.
        
5. Centro de administración de Active Directory - 'dsac.exe'
    - Acceso: Escribe 'dsac.exe' en el comando "Ejecutar".
    - Función: Es una interfaz gráfica moderna para administrar AD que combina las funcionalidades de varias otras herramientas de MMC.
        
6. DNS - 'dnsmgmt.msc'
    - Acceso: Escribe 'dnsmgmt.msc' en el comando "Ejecutar".
    - Función: Administra el servidor de DNS del dominio, lo cual es crucial para la funcionalidad de AD, ya que AD depende en gran medida de una configuración de DNS correcta para resolver nombres y localizar servicios y servidores.
```

## Otras funcionalidades esenciales de Active Directory incluyen:

```bash 
7. Control de Servicios - 'services.msc'
    - Acceso: Escribe `services.msc` en el comando "Ejecutar".  
    - Función: Gestiona los servicios en ejecución en un servidor, lo cual incluye los servicios de Active Directory como el servicio de Directorio de Active Directory (NTDS) o el servicio de replicación de File Replication Service (FRS) o Distributed File System (DFS).
        
8. Visor de Eventos - 'eventvwr.msc'
    - Acceso: Escribe 'eventvwr.msc' en el comando "Ejecutar".  
    - Función: Permite a los administradores ver eventos y registros de seguridad, lo cual es esencial para el monitoreo del rendimiento y la seguridad del AD.
        
9. Administrador de DHC - 'dhcpmgmt.msc'
    - Acceso: Escribe 'dhcpmgmt.msc' en el comando "Ejecutar".
    - Función: Administra el protocolo de configuración dinámica de host (DHCP) que asigna direcciones IP a dispositivos en la red.
        
10. Administrador de Certificados - 'certmgr.msc'
    - Acceso: Escribe 'certmgr.msc' en el comando "Ejecutar".
    - Función: Gestiona certificados para garantizar la seguridad en la comunicación entre servidores y clientes dentro del dominio.
        
11. Conector de Almacenamiento de Información de Internet (IIS) - 'inetmgr'
    - Acceso: Escribe 'inetmgr' en el comando "Ejecutar".
    - Función: Gestiona las configuraciones de servidores web que son importantes si estás utilizando servicios web en tu infraestructura de AD.
        
12. Servicios de Escritorio Remoto - 'tsadmin.msc'
    - Acceso: Escribe 'tsadmin.msc' en el comando "Ejecutar" (solo disponible en versiones anteriores de Windows Server
```