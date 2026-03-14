# PowerUP

Tags: #Windows #PowerUP

* [PowerUp](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1)

PowerUp.ps1 es un script desarrollado en PowerShell que pertenece a la suite PowerSploit, una colección de módulos diseñada para apoyar a los pentesters en las etapas de post-explotación y evaluación de seguridad en entornos Windows. Su objetivo principal es automatizar el análisis de configuraciones de seguridad y detectar posibles vectores que permitan la elevación de privilegios. Esta herramienta es ampliamente empleada tanto en pruebas de penetración como por equipos de Red Team, ya que facilita la identificación de debilidades y vulnerabilidades en la configuración de sistemas Windows que podrían ser aprovechadas para obtener mayores privilegios.

```powershell 
❯ Import-Module .\PowerUp.ps1       # Importamos el modulo 
❯ Invoke-AllChecks                  # Ejecutamos el submodulo y comenzamos con el escaneo 

❯ Get-ModifiableService             # lista los servicios los cuales pueden ser configurados 
❯ Get-ServiceUnquoted               # Unquoted Service Path 


Nota:
	1. Si quieres ofuscar el 'PowerUp.ps1' ir a la linea '2640' y eliminar el contenido de la variable '$B64Binary = ""'
```

Entre las características y capacidades de 'PowerUp.ps1', se incluyen:

- Enumeración de servicios con rutas no citadas: Identifica servicios donde la ruta del ejecutable no está entre comillas y contiene espacios, lo que podría permitir la inserción de ejecutables maliciosos en la ruta.
- Permisos de ejecutables de servicios: Comprueba si el usuario actual tiene permisos para modificar archivos de servicios de Windows que luego podrían ser ejecutados con privilegios elevados.
- Permisos de servicio: Evalúa si el usuario actual tiene la capacidad de modificar servicios de Windows o su configuración.
- Búsqueda en la variable de entorno '%PATH%': Busca ubicaciones en la variable de entorno '%PATH%' que podrían ser usadas para la inyección de DLLs maliciosas.
- Clave de registro 'AlwaysInstallElevated': Comprueba si esta clave de registro está configurada para permitir la instalación de programas con privilegios elevados.
- Credenciales de autologon: Busca credenciales almacenadas en el registro que permiten el inicio de sesión automático.
- Configuraciones de Autorun y archivos modificables: Identifica configuraciones de autorun y archivos del sistema que son modificables y podrían ser abusados para ejecutar código al reiniciar el sistema o al iniciar sesión.
- Tareas programadas: Revisa los archivos y configuraciones de tareas programadas que podrían ser modificadas para obtener ejecución de código con privilegios.
- Archivos de instalación desatendidos: Busca archivos de instalación desatendida que pueden contener credenciales en texto plano.
- Cadenas cifradas en archivos 'web.config': Identifica cadenas de configuración cifradas que podrían incluir información sensible.
- Contraseñas de pools de aplicaciones y directorios virtuales: Busca contraseñas cifradas asociadas con pools de aplicaciones y directorios virtuales en IIS que podrían ser descifradas.
- Contraseñas en texto plano en archivos 'SiteList.xml' de McAfee: Busca archivos de configuración de McAfee que pueden contener contraseñas en texto plano.
- Preferencias de Política de Grupo en caché: Revisa si hay archivos .xml de Política de Grupo que contienen contraseñas o configuraciones que podrían ser explotadas.

# Validaciones por PowerUp

- Current privileges
- Unquoted service paths
- Service executable permissions
- Service permissions
- %PATH% for hijackable DLL locations
- AlwaysInstallElevated registry key
- Autologon credentials in registry
- Modifidable registry autoruns and configs
- Modifiable schtask files/configs 
- Unattended install files  
- Encrypted web.config strings
- Encrypted application pool and virtual directory passwords
- Plaintext passwords in McAfee SiteList.xml
- Cached Group Policy Preferences .xml files


