# WinPEAS 

Tags: #WinPEAS #Windows

* [WinPeas](https://github.com/peass-ng/PEASS-ng/blob/master/winPEAS/winPEASexe/README.md)

WinPEAS es una herramienta de enumeración diseñada para entornos Windows, enfocada en auditorías de seguridad y detección de posibles vías de escalación de privilegios. Forma parte de la suite PEAS (Privilege Escalation Awesome Scripts) y está escrita en batch y C#. Su diseño ligero permite ejecutarla sin instalaciones ni configuraciones complejas, automatizando la recopilación de información que podría revelar vulnerabilidades o configuraciones inseguras aprovechables por un atacante.

```bash 
❯ winPEASany.exe             # Ejecutamos la version no ofuscada (normal)
❯ winPEASany_ofs.exe         # Ejecutamos la version ofuscada
```

# Funciones y capacidades de WinPEAS incluyen:

- Información del Sistema y Configuración: WinPEAS recopila información general sobre el sistema operativo, versiones, configuraciones de red, y servicios en ejecución.   
- Usuarios y Grupos: Enumera los usuarios y grupos locales, así como los privilegios y derechos de usuario.
- Credenciales: Busca contraseñas y claves almacenadas en el sistema, incluyendo posibles archivos de configuración y bases de datos.
- Permisos de Archivos y Directorios: Revisa los permisos de archivos y directorios críticos para ver si hay configuraciones inseguras que permitan la modificación por usuarios no privilegiados.
- Tareas Programadas: Enumera las tareas programadas y sus configuraciones para identificar posibles scripts o binarios que puedan ser modificados.
- Servicios y Drivers: Enumera los servicios y drivers, verificando aquellos que tienen configuraciones inseguras o permisos excesivos.
- Información de Registro: Busca entradas de registro susceptibles de abuso, como las configuraciones 'Autorun' y las claves de registro 'AlwaysInstallElevated'.
- Vulnerabilidades Conocidas: Comprueba contra una base de datos de vulnerabilidades conocidas que podrían estar presentes en el sistema basándose en la versión del software instalado.
- Escaneo de Red: Recopila información sobre recursos compartidos de red y conexiones establecidas.
- Auditoría de Seguridad: Incluye chequeos para muchas configuraciones de seguridad, como la configuración de UAC (User Account Control), firewalls y políticas de seguridad.
