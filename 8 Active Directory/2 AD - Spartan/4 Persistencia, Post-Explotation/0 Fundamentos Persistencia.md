# Fundamentos de persistencia 

Tags: #AD #Windows 

La persistencia y la post-explotación en Active Directory (AD) son elementos clave en el pentesting y en las operaciones ofensivas de ciberseguridad. Estas etapas se producen una vez que se ha logrado acceso a un sistema o red, con el fin de asegurar el acceso continuo y seguir explotando la red comprometida. A continuación, se detallan varios aspectos técnicos relacionados con la persistencia y la post-explotación en AD:

```bash 
1. Conceptos de Persistencia: La persistencia se refiere a la capacidad de mantener el acceso a un sistema o red después de cerrar la sesión inicial o reiniciar el sistema. Esto es esencial para operaciones continuas y para evitar tener que comprometer el sistema nuevamente.
    
2. Técnicas de Persistencia: Las técnicas de persistencia en AD pueden incluir la creación de cuentas de usuario ocultas, el uso de tareas programadas, la modificación de scripts de inicio, y la implantación de puertas traseras en software o sistemas operativos.
    
3. Manipulación de Políticas de Grupo (GPOs): Las GPOs en AD pueden ser modificadas para ejecutar scripts o configuraciones que aseguren el acceso continuo del atacante a la red.
    
4. Uso de Servicios y Procesos del Sistema: Los atacantes pueden insertar módulos maliciosos o modificar servicios existentes para garantizar que sus herramientas se ejecuten continuamente.
    
5. Registro en el Sistema y Evasión de Detección: Durante la persistencia, es vital evitar la detección. Esto puede implicar manipular o desactivar registros y evadir soluciones de seguridad como antivirus y sistemas de detección de intrusiones.
    
6. Post-Explotación y Movimiento Lateral: Una vez establecida la persistencia, los atacantes suelen realizar actividades de post-explotación, que incluyen el movimiento lateral para comprometer más sistemas y la recolección de datos sensibles.
    
7. Exfiltración de Datos: La post-explotación también puede implicar la exfiltración de datos críticos, como información de propiedad intelectual, datos financieros o información personal.
    
8. Manipulación de ACLs y Permisos: Cambiar las listas de control de acceso y los permisos en objetos de AD puede permitir a los atacantes acceder a recursos de forma persistente o modificar configuraciones de seguridad.
    
9. Abuso de Funciones y Protocolos de AD: La explotación de funciones normales de AD (como la replicación o la delegación) puede ser usada para mantener acceso y control sobre la red.
    
10. Herramientas y Automatización: La persistencia y la post-explotación a menudo implican el uso de herramientas especializadas y scripts automatizados para gestionar el acceso y realizar tareas de manera eficiente.
    
11. Planificación y Adaptación: Cada entorno de AD es único, y las estrategias de persistencia y post-explotación deben adaptarse al contexto específico y a la configuración de la red objetivo.
```