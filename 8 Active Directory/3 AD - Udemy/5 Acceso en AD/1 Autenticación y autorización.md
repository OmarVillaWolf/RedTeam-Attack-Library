# Autenticación y autorización en Windows 

Tags: #AD 

* [Access Tokens](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-tokens)
* [Autenticación en Windows](https://learn.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication#BKMK_CrentialInputForUserLogon)

## LSA Logon Session 

* [Logon Session - Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/logonsessions)

Cuando un usuario se autentica correctamente, el paquete de autenticación crea una 'logon session' y devuelve información a la autoridad de seguridad local(LSA).
El módulo LSA usa esta información para crear un 'Access token' para el nuevo usuario. Esto token incluye, entre otras cosas, un identificador único local (LUID) para la 'logon session', denominado identificador de inicio de sesión.   

```powershell
# Ejecutar como usuario admin
❯ .\logonsession.exe     # Mirar todas las 'logon sessions' que se han establecido en el equipo 
❯ .\logonsession.exe -p  # Mirar los procesos asociados a las 'logon sessions'

Cuando se observa un:
- 'Logon Type: Interactive' quiere decir que en el equipo en la memoria del LSA las credenciales están almacenadas 
- 'Logon Type: Network' quiere decir que es una sesión 'No interactiva' y que se ha hecho una autenticación a nivel de red por lo que las credenciales de autenticación no estarian almacenadas en el equipo
```

## Tipos de autenticación 

1. **Autenticación Interactiva** 
- La auntenticación es interactiva cuando se solicita al usuario que proporcione información de inicio de sesión. 
- La autoridad de seguridad local (LSA) realiza una autenticación interactiva cuando un usuario inicia sesión a través de la interfaz de usuario de GINA.
- Si la autenticación se realiza correctamente, comienza la 'loggon session' del usuario y se guarda un conjunto de credenciales de inicio de sesión para futuras referencias.  

2. **Autenticación No-Interactiva**
- La autenticación no interactivas solo se puede usar después de que se haya realizado una sesión interactiva.
- El usuario no introduce datos de inicio de sesión, en su lugar, se usan las credenciales establecidas y almacenadas previamente.
- Se utiliza para conectar a varias máquinas y servicios en red sin tener que volver a introducir credenciales. 

## Access Token 

* [Process Explorer - Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/process-explorer)

1. Cuando un usuario inicia sesión, el sistema comprueba la contraseña del usuario comparándolo con la información almacenada en una base de datos de seguridad. Si se autentica correctamente, el sistema genera un token de acceso. Cada proceso ejecutado en nombre de este usuario tiene una copia de este token de acceso. Un token de acceso es un objeto que describe el contexto de seguridad de un proceso o subproceso. La información de un token incluye la identidad y los privilegios de la cuenta de usuario asociada al proceso o subproceso. El sistema usa un token de acceso para identificar al usuario cuando un subproceso interactúa con un 'securable object' o intenta realizar una tarea del sistema que requiere privilegios.
2. Cada proceso tiene un token principal que describe el contexto de seguridad de la cuenta de usuario asociada al proceso. De forma predeterminada, el sistema usa el token principal cuando un subproceso del proceso interactúa con un 'securable object'. Además, un subproceso puede suplantar una cuenta de cliente. La suplantación permite que el subproceso interactúe con 'securable objects' mediante el contexto de seguridad del cliente. Un subproceso que suplanta a un cliente tiene in token principal y un token de suplantación.

```powershell
# Ejecutar como usuario admin
❯ .\procexp64.exe     # Mirar el token y los procesos 
```
