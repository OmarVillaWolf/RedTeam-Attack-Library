# Herramientas y métodos de Post-Explotación 

```bash 
1. Mimikatz:
    - Uso: Es una herramienta de post-explotación conocida por su capacidad para extraer contraseñas, hashes, tickets Kerberos y otros datos de la memoria de Windows.
        
    - Ataques famosos: Pass the Hash, Pass the Ticket, Golden y Silver Tickets.
        
2. PsExec:
    - Uso: Herramienta de línea de comandos que permite la ejecución de procesos en sistemas remotos. Es usada para moverse lateralmente a través de una red.
        
    - Ataques famosos: Ejecución remota de malware o herramientas de post-explotación en máquinas dentro de una red.
        
3. Metasploit Framework:
    - Uso: Suite de herramientas para el desarrollo y ejecución de exploits contra objetivos remotos, que también incluye módulos de post-explotación. 
    - Ataques famosos: Meterpreter para mantener sesiones persistentes y la realización de ataques como el dumping de credenciales y la elevación de privilegios.
        
4. Empire:
    - Uso: Framework de post-explotación que usa PowerShell y Python para su arsenal de herramientas.
    - Ataques famosos: Creación de listeners, dumping de credenciales, ejecución de código y explotación de configuraciones débiles.
        
5. BloodHound:
    - Uso: Utiliza el modelo de ataque basado en relaciones de Active Directory para desvelar patrones de relaciones y flujos de permisos que son críticos para la comprensión del atacante sobre el movimiento lateral y la escalada de privilegios.
    - Ataques famosos: Visualización de caminos de ataque dentro de una red de Active Directory.
        
6. Powershell Empire o PowerShell Scripts:
    - Uso: Uso de scripts PowerShell para automatizar tareas de post-explotación y establecer persistencia.
    - Ataques famosos: Inyección de backdoors, creación de tareas programadas y manipulación de procesos y servicios para mantener el acceso.
```

# Técnicas de persistencia 

```bash 
1. Registry Keys:
    - Descripción: Modificación o adición de claves de registro específicas para que el código malicioso se ejecute al iniciar el sistema.
        
2. Service Creation:
    - Descripción: Creación de servicios de Windows para ejecutar malware de forma continua como un proceso del sistema.
        
3. Scheduled Tasks:
    - Descripción: Programación de tareas para ejecutar malware en momentos específicos o bajo ciertas condiciones.
        
4. WMI Event Subscriptions:
    - Descripción: Uso de Windows Management Instrumentation para ejecutar scripts o binarios cuando se cumplen ciertas condiciones.
        
5. Startup Folder:
    - Descripción: Colocación de scripts o enlaces en la carpeta de inicio de Windows para ejecución tras el inicio de sesión de un usuario.
        
6. Account Manipulation:
    - Descripción: Creación de cuentas de usuario ocultas o modificaciones en cuentas existentes para garantizar el acceso.
```