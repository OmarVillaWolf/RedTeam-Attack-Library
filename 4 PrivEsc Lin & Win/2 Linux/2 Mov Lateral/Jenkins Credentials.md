# Movimiento lateral — Jenkins

Tags: #PrivEsc #MovimientoLateral 

Jenkins es una herramienta de automatización usada para compilar, probar y desplegar software. Por ejemplo, puede ejecutar pruebas automáticamente cada vez que alguien actualiza el código. Se instala en un servidor y se administra normalmente desde una interfaz web.
## Jenkins Credentials 
```bash 
/var/lib/jenkins/config.xml
# Configuración global de Jenkins.

/var/lib/jenkins/credentials.xml
# Credenciales almacenadas y referencias a credenciales.

/var/lib/jenkins/secrets/
# Claves y secretos internos utilizados por Jenkins.

/var/lib/jenkins/.ssh/
# Claves y configuración SSH del usuario Jenkins.

/var/lib/jenkins/users/
# Configuración y datos de los usuarios de Jenkins.

/var/lib/jenkins/jobs/
# Directorio donde se almacenan los Jobs/Pipelines.

/var/lib/jenkins/jobs/*/config.xml
# Configuración individual de cada Job; puede contener repositorios,
# variables, credenciales asociadas y comandos.

/var/lib/jenkins/workspace/
# Archivos generados o descargados durante la ejecución de Jobs.

/var/lib/jenkins/nodes/
# Configuración de agentes/nodos de Jenkins.

/var/lib/jenkins/plugins/
# Plugins instalados y sus archivos/configuraciones.

/var/lib/jenkins/logs/
# Logs de Jenkins que pueden revelar información útil.

/var/lib/jenkins/war/
# Archivos de la aplicación Jenkins desplegada.
```