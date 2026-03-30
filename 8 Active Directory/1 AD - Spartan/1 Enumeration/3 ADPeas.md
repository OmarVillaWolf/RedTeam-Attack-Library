# ADPeas

Tags: #AD #ADPeas #Powershell 

ADPeas es una herramienta en PowerShell diseñada para enumerar y analizar entornos de Active Directory, facilitando la identificación de configuraciones inseguras, relaciones de confianza, permisos excesivos y posibles vectores de escalación de privilegios. Es ampliamente utilizada en auditorías y pruebas de seguridad por su capacidad para automatizar la recolección de información clave y detectar vulnerabilidades en dominios Windows.

* [ADPeas](https://github.com/61106960/adPEAS)

```powershell 
❯ IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/61106960/adPEAS/main/adPEAS.ps1')

❯ Invoke-adPEAS    # Módulo para hacer la enumeración automatizada
```

## Saliendo del contexto de usuario local

```bash 
# Esto se hace cuando el usuario local no puede interactuar con el dominio y por lo tanto no podría ejecutar ADPeas u otras herramientas en Powershell para enumerar el dominio. 


PS C:\Windows\system32> net group /domain         # Enumerar el dominio 
The request will be processed at a domain controller for domain domain.com.   # Sale este error 

System error 5 has occurred.

Access is denied.
```

* [PsExec64.exe](https://github.com/Spartan-Cybersecurity/CPAD-Tools)

```bash 
❯ .\PsExec64.exe -i -s cmd.exe       # Emigrar en una sesion RDP al usuario 'NT AUTHORITY SYSTEM' para asi poder interactura con el dominio. 
```

* [Intro a sysinternals](https://books.spartan-cybersec.com/cpad/introduccion-a-la-evasion-de-defensas/introduccion-a-sysinternals)

```bash 
'PsExec' es una herramienta ligada a la suite de Sysinternals de Microsoft y permite la ejecución de procesos en sistemas remotos. Es particularmente útil para administradores de sistemas, pero también es conocido por ser utilizado por atacantes o pentesters para moverse lateralmente a través de una red.

1. Contexto de Autenticación: Cuando interactúas con Directorio Activo (AD), lo haces a través de un contexto de autenticación, que esencialmente dicta qué derechos y permisos tiene el usuario o proceso actual. Si estás ejecutando comandos en una máquina comprometida con un contexto local (por ejemplo, una shell reversa que no tiene un token de autenticación para AD), es posible que no puedas consultar o interactuar con el AD aunque estés en un dominio.
    
2. Cambiar Contexto: Usando 'PsExec', puedes ejecutar comandos o programas en el contexto de otro usuario, o incluso en el contexto de SYSTEM si tienes los permisos necesarios. Al hacerlo, estás esencialmente "cambiando" tu contexto de autenticación, lo que puede darte los derechos necesarios para interactuar con AD.

Notas:
	1. Aunque no es _estrictamente necesario_ usar 'PsExec' para salir del contexto de usuario local y enumerar AD (hay otras técnicas y herramientas que pueden lograr lo mismo), 'PsExec' es una herramienta versátil que permite a los atacantes y pentesters cambiar de contexto de autenticación y moverse lateralmente a través de una red. Por lo tanto, es una herramienta útil en escenarios donde se necesita ampliar el alcance o cambiar el contexto para interactuar con AD.
```

