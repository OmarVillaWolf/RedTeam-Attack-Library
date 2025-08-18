
Tags: #Linux #OverTheWire #Comandos 

## Conexión SSH

```bash 
❯ ssh user@IP -p 22                             # Conexion remota a un servidor por SSH
❯ sshpass -p <‘password’> ssh user@IP -p 22     # Colocar desde el inicio la passwd al conectarse por SSH

❯ export TERM=xterm                             # Para usar el Ctrl + l y limpiar la pantalla
```

## Lectura de archivos especiales 

```bash 
❯ cat /home/omar/-                          # Forma de leer los archivos con caracteres especiales como '-' pasandole la ruta absoluta
❯ cat /home/omar/*                          # Forma de leer los archivos con caracteres especiales
❯ cat ./-                                   # Forma de indicar que queremos leer un archivo especial '-', indicando que se encuentra en el directorio actual
❯ cat 'spaces in this file'                 # Con '' podemos leer archivos que su nombre tenga espacios, tambien lo podemos hacer haciendo click en TAB para autocompletar
❯ cat spaces\ in\ this\ file                # Se hace esto cuando tu archivo cuenta con espacios y debemos 'escapar'
❯ cat s*                                    # Queremos leer un archivo que empieza con s y tiene una cadena de caracteres (*=wildcard)
```

```bash 
❯ grep -r "\w" 2>/dev/null | tail -n 1      
	# r = forma recursiva 
	# w = palabra 
	# tail sirve para colocar en este caso la ultima linea del codigo 
	# n = numero de la linea que queremos ver, lo del dev/null es porque hay mucha informacion que no tenemos permiso y asi logramos que no nos salga.
	
❯ grep -r "\w" 2>/dev/null | tail -n 1 | awk '{print $2}' FS=':'            # Filtramos y miramos la ultima linea pero ahora le decimos que queremos quedar con el segundo argumento tomando como delimitador ":" (FS=delimitador)

❯ grep -r "\w" 2>/dev/null | tail -n 1 | cut -d ':' -f 2                    # Filtramos y miramos la ultima linea, para despues cortar con el delimitador ':' y quedarnos con el segundo argumento (d=delimitador, f=file o argumento)

❯ grep -r "\w" 2>/dev/null | tail -n 1 | tr ':' ' ' | awk '{print $2}'      # Filtramos y miramos la ultima linea del codigo para despues sustituir ':' por espacios y al final nos quedamos con el segundo argumento (tr=aplicar sustituciones)

❯ grep -r "\w" 2>/dev/null | tail -n 1 | tr ':' ' ' | awk 'NF{print $NF}'   # Hacemos lo mismo que arriba pero referenciamos que nos queremos quedar con el ultimo argumento de la linea con NF
```

## Directorio y archivos ocultos

```bash 
❯ file .hidden          # Muestras que tipo de archivo es, tipo file, data, etc… (ASCII Text)

❯ cat .hidden           # Mostramos el contenido de un archivo oculto, colocando correctamente el nombre del archivo

❯ find . -type f | grep ‘hidden’ | xargs cat # Encontramos el archivo de tipo file (f=file), despues lo filtramos con grep y al final con xargs que nos ayuda a colocar otro comando le aplicamos un cat

❯ find . -type f | grep -vE “bashrc|profile|logout” | xargs cat    # Encontramos nombres que contengan ‘.’ de tipo archivo para despues filtar las siguientes alabras y quitarlos y al final le hacemos un cat al archivo resultante (f=tipo file, v=Quitas un archivos, E=Quitas mas archivos juntos)
```

## Detección del tipo y formato de archivos

```bash 
❯ find . -type f | grep “\-file” | xargs file   # Buscamos archivos tipo f=file, luego con grep lo filtramos indicando \-file, al final le decimos que tipo de archivo son y miramos con cat el que tenga ASCII text

❯ cat ./inhere/-file07                          # Asi miramos el contenido del archivo llamado 'file07' con su ruta absoluta
```

## Búsquedas precisas de archivos

```bash 
❯ find . -type f ! -executable -size 1033c | xargs cat | xargs    # Buscamos desde aquí archivos de tipo file, que no sea ejecutable y que tenga un tamano especifico (!=es negacion, c=para colocar el tamano que queremos buscar), el segundo xargs es porque aveces tenemos problemas con el output y no los muestra feo, y con ese segundo se arregla.
❯ find . -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null | xargs cat   # Usamos find para encontrar un archivo tipo file, que el usuario se llame bandit7, que su grupo asignado sea bandit6, que contenga un tamano especifico y los errores no los muestre en pantalla y al final le podemos hacer un cat al archivo encontrado
```

## Método de filtrado de datos

```bash 
❯ cat data.txt | grep “millionth” | awk ‘NF{print $NF}’        # Buscamos en ese archivo la palabra filtrada por grep, al final con awk le decimos que nos queremos quedar la parte final del argumento

❯ cat data.txt | grep “millionth” | awk ‘NF{print $2}’         # Hace lo mismo de arriba pero le decimos a awk que queremos printiar el segundo argumento

❯ cat data.txt | grep “millionth” | xargs | cut -d ‘ ’ -f 2    # Hace lo mismo de arriba pero xargs te ayuda a que sea mas compacto, y asi podamos usar cut de una manera mas eficiente (d=delimiter) y le decimos que nos quedamos con el seguundo argumento

❯ cat data.txt | grep “millionth” | xargs | tr ‘ ‘ ‘\n’ | tail -n 1   # (tr=sustitucion en este caso de espacios por salto de linea) y con tail le decimos que nos queremos quedar cone la ultima linea

❯ Sort data.txt | uniq -u                                       # Le decimos que queremos un ordenamiento, para despues con el siguiente comando le decimos que queremos la unica linea que no se repite (u=queremos lineas unicas)
```

## Interpretación de archivos binarios

```bash 
❯ strings data.txt | grep ‘=\=\=’ | tail -n 1 | awk ‘NF{print $NF}’   # Sirve para listar las cadenas de caracteres imprimibles de un archivo (Cuando el archivo no sea ASCII y sea DATA) y ya despues hacemos el filtrado correspondiente
```

## Codificación y decodificación en base64

```bash 
# Es un sistema de numeracion posicional que usa 64 como base. Es la mayor potencia que puede ser representada usando unicamente los caracteres imprimibles de ASCII. Esto ha propiciado su uso para codificacion de correos electronicos, PGP y otras aplicaciones.

❯ echo ‘Hola esto es una prueba’ | base64        # Con esto transformas una cadena en base 64
❯ cat /etc/hosts | base64 -w 0                   # Si tenemos multiples lineas y queremos que el resultado nos lo coloque en una sola linea, debemos colocar el ultimo parametro w y 0
❯ echo ‘SG9sYSHohbfkbLIKBJBYUF54H’ | base64 -d   # Con el parametro (d=decode) haces un decode a la cadena que tiene base 64 y te regresa el contenido en formato texto que podemos leer
❯ cat data.txt | base64 -d                       # Con eso hacemos que el conteno de ese archivo que esta en base64 lo podamos leer ya que lo estamos decodificando
```

## Cifrado Cesar y uso de tr para la traducción de caracteres

```bash 
Puedes usar la pagina 'rot13.com' donde tu le pasas lo que quieres decifrar y ella te la rotara las 13 posiciones. Y ahi mismo puedes encontrar diferentes rotaciones.
```

## Descompresor recursivo

```bash 
# Descargamos el archivo desde el video de Hack4u llamado 'Decompressor.sh' para descomprimir mas rápido cualquier archivo

❯ cat /etc/host | xxd                              # Convertimos en hexadecimal ese output
❯ cat /etc/host | xxd -ps | xargs | tr -d ' '      # Para unicamente ver la parte hexadecimal, (ps=quitar lo que no nos interesa, xargs=lo compactamos en una sola linea,tr=quitamos los espacios)

❯ cat data | xxd -r | sponge data                  # Hacemos el proceso inverso y lo metemos en el mismo archivo llamado data

Cuando manipulas un archivo, lo filtras y al final quieres mandar el output al mismo archivo, no debes de poner '>' o '>>'
❯ cat test | awk '{print $3}' | sponge test        # Solo asi mandamos el output al mismo archivo, (sponge=evitara que se vacie el archivo en Linux)
```

## Manejo de partes de claves y conexiones SSH

```bash 
# Para instalar el ssh en Linux
❯ apt install openssh 
❯ apt install ssh

❯ sudo systemctl start sshd         # Habilitamos el servicio SSH (d=demon)
❯ sudo systemctl stop sshd          # Habilitamos el servicio SSH
❯ ssh-keygen                        # Creamos una clave publica ('id_rsa.pub') y una clave privada ('is_rsa'), le damos enter a todo sin colocar nada

# Debemos de convertir o copiar nuestra clave publica del usuario atacante en un archivo llamado **authorized_keys** para podernos conectar. Este archivo lo debemos de incorporar en la maquina victima.
❯ cp id_rsa.pub authorized_keys
❯ ssh omar@localhost                # Asi podremos conectarnos a ssh sin proporcionar passwd

❯ ssh-copy-id -i id_rsa omar@localhost     # Le pasamos la clave privada, y le decimos que nos queremos conectar al localhost, proporcionamos la passwd y la convierte en autorizada.
La clave privada la debe de tener el atacante
❯ ssh -i id_rsa omar@ip                    # Asi deberia entrar sin proporcionar passwd
```

## Uso de Netcat para realizar conexiones

```bash 
❯ nc localhost 30000              # Nos conectamos al puerto 30000 del localhost
 
❯ netstat -nat                    # Para mirar los puerto abiertos
❯ ss -nltp                        # Para mirar los puerto abiertos

❯ cat /proc/net/tcp               # Podemos ver el archivo y los puertos abiertos, solo que debemos de filtrar y convertirlos de hex a dec
❯ lsof -i:22                      # Para que me de una idea, de que esta corriendo en ese puerto (l=ele)
❯ ps -faux                        # Podemos ver todos los procesos corriendo en el sistema
```

## Uso de conexiones encriptadas

```bash 
❯ which nc                        # Listas la ruta absoluta de algun comando y asi te das cuenta si esta instalado o no, en este caso es Netcat
❯ ncat --ssl 127.0.0.1 30001      # Es como un Netcat y podenos conectar pos SSL, en este caso nos conectamos al localhost, por el puerto 30001, solo debemos e proporcionar la passwd y listo
```

## Creando nuestro propio escáner en Bash

```bash 
❯ nvim PortScan.sh           # Creamos el file y le damos permisos de ejecucion con 'chmod +x PortScan.sh'
❯ vim HostScam.sh            # Creamos un file para buscar host y le damos permisos de ejecucion con 'chmod +x HostScan.sh'

❯ mktemp -d                  # Nos crea un directorio temporal de trabajo.

Una vez descubierto los puerto podemos conectarnos a cada uno de ellos por el SSL
❯ ncat --ssl 127.0.0.1 30001 # Es como un Netcat y podenos conectar pos SSL, en este caso nos conectamos al localhost, por el puerto 30001, solo debemos e proporcionar la passwd y listo
❯ ssh -i id_rsa user@ip      # Las claves privadas (id_rsa) deben de tener el privilegio 600 y eso lo hacemos con 'chmod 600 id_rsa' (i=identity)
```

## Detección de diferencias entre archivos

```bash 
Debemos de tener al menos dos archivos para poder hacer la diferencia entre ellos
❯ wc -l file           # Para ver cuantas lineas tiene el archvio llamado file (l=ele)
❯ diff file1 file2     # Sirve para detectar las diferencias de los dos archivos llamados file1 y file2
```

## Ejecución de comandos por SSH

```bash 
A través del archivo de configuración '.bashrc' o '.zshrc', es posible definir una serie de acciones a llevar a cabo a la hora de obtener una consola interactiva, en este caso tras ingresar por SSH.

❯ sshpass -p 'passwd' ssh user@ip -p 2220 whoami        # Podemos inyectar un comando antes de que nos procese el SSH, el puerto al que nos conectamos es el 2220, primera y segunda p (p=passwd, p=port), en este caso es whoami

❯ sshpass -p 'passwd' ssh user@ip -p 2220 bash          # Podemos inyectar un comando, buscando a que nos de una consola bash 
```

## Abusando del privilegio SUID para migrar de usuario

```bash 
❯ bash -p        # Para abusar de un privilegio SUID
```

## Conexiones 

```bash 
❯ ./suconnect <port>   # Te conectas a un puerto del localhost
```
