# Linux 

Tags: #Linux #Comandos

## Comandos importantes en Cloud 

```bash 
❯ env           # Muestra las variables de entorno, a veces hay información sensible 

Variables de entorno: 
1. ORYX_ENV_TYPE = AppService         # Significa que es un aplicativo Web 
2. APPSETTING_WEBSITE_SITE_NAME =  Domain1-company   # Muestra el nombre del 'AppService'
```

## Comandos terminal Linux

```bash 
# Activar SSH en Kali 

❯ sudo update-rc.d -f ssh remove
❯ sudo update-rc.d ssh defaults
❯ sudo update-rc.d ssh enable
❯ sudo reboot                     # Reiniciamos Kali

❯ sudo systemctl status ssh       # Mirar si se esta ejecutando SSH
```

```bash
# Activar RDP en Kali

❯ sudo apt install xrdp -y        # Instalamos RDP
❯ sudo adduser xrdp ssl-cert      # Incluimos el usuario 'xrdp' en el certificado SSL
❯ sudo systemctl enable xrdp      # Activamos el servicio RDP 
❯ sudo reboot                     # Reiniciamos Kali
```

```bash 
❯ service apache2 start           # Iniciamos el servicio de apache
❯ service apache2 stop            # Paramos el servicio de apache 

❯ sudo systemctl status apache2   # Mirar si se esta ejecutando Apache
```

```bash 
❯ apt update             # Actualizar el SO en Kali
❯ apt upgrade 
❯ apt full-upgrade     

❯ parrot-upgrade         # Actualizar el SO en Parrot 
```

```bash 
❯ pip3 install pwn --break-system-packages    # Instalar las pwntools 
```

```bash
/tmp                     # Directorios con capacidad de lectura y escritura en Linux       
/var/tmp 
/dev/shm      
/temp                    # Directorios con capacidad de lectura y escritura en PHP

❯ Ctrl + u               # Miramos el codigo fuente de la pagina 
❯ Ctrl + Shift + c       # Abrimos el 'Inspect' de la pagina 
```

```bash
/etc/hosts               # Para agregar algun dominio existente
/etc/passwd              # Ruta de las cuentas de usuario
/etc/shadow              # Hashes encriptados de passwds de los usuarios y solo puede acceder el usuario root 
/etc/group               # Ruta de configuracion del archivo de grupos
/etc/shells              # Podemos ver las diferentes shells que hay 
/etc/ssh/sshd_config     # Podemos ver las configuraciones de ssh y ver si el usuario root se puede conectar 
```

```bash 
❯ Ctrl + k                 # Quitas todo lo que se encuentre a la derecha de donde estas posicionado

❯ kitty +kitten icat <image.jpg>     # Muestras la imagen por consola 
```

```bash
❯ echo ‘ ’ > ~/.zsh_history     # Para borrar el historial en la zshrc
```

```bash
❯ !$            # Referencias el ultimo argumento que hayamos puesto en la consola 
```

```bash 
❯ pushd /usr/share/             # Para ir a un directorio especifico, funciona como el comando 'cd'
❯ popd                          # Regresamos al directorio donde nos encontrabamos al momento de usar 'pushd'
```

```bash 
❯ scrub -p dod <file>              # Primero haces esto y despues ejecutas el comando de abajo

❯ shred -zun 10 -v <file>          # Borrar un archivo, pero que el borrado sea mas complejo y asi no dejaremos evidencia, a mas valor que el 10 es mejor, ademas de borrarlo evita que se pueda recuperar
❯ shred -zun 10 -v file*           # Borrar todos los archivos que empiecen con una palabra en especifico
```

```bash 
❯ watch -n 1 ls -l /bin/bash      # Monitorizar la bash cada segundo con el siguiente comando y ver cuando cambia a SUID
```

```bash 
❯ extractPorts <filename>         # Herramienta que nos ayuda extraer los puerto de la captura de Nmap y copiarlos a la clipboard 
```

```bash
❯ ping -c 1 ❮IP❯                  # Para saber si la maquina esta activa o no (ttl=64 Linux, ttl=128 Windows)

	# IP = IP Address de la maquina target 
	# c = Numero de pings a ejecutar

❯ ping -c 1 < TARGET IP> -R       # Verificar si tenemos conexion a una IP mandando numeros de ping especificos 
	
	# R = Traceroute, solo aplica en maquinas Linux
	# c = Numero de ping 

❯ fping -I ens33 -g <IP/24> -a 2>/dev/null   # Manda un ping a multiples IP
```

```bash
❯ ls -la                  # Podemos observar todos los archivos del dir inclyendo los ocultos 
```

```bash
❯ whoami                  # Miramos el nombre del usuario
```

```bash
❯ lsb_release -a          # Miramos algunas caracteristicas de la maquina Linux 
```

```bash
❯ ip a                    # Para ver las interfaces e IPs de una maquina
❯ ifconfig                # Para saber que IP hay en mi maquina 
❯ ifconfig docker0        # Miramos la IP de la interfaz de Docker
```

```bash
❯ file file.txt           # Mostrar que tipo de archivo es gracias a los magic numbers

❯ strings file.exe        # Extraer y mostrar las cadenas de texto (secuencias de caracteres imprimibles) que se encuentran dentro de un archivo binario

❯ radare2 file.exe        # Inspeccionar las cadenas de texto en un binario 
	❯ aaa                # Mirar todas las funciones existentes 
	❯ afl                # Listar las funciones existentes 
	❯ s main             # Sincronizar con 'main' y mirar a bajo nivel 
```

```bash
❯ locate nc.exe       # Buscar el Netcat para Windows. Si no funciona se debe de actualizar la DB
❯ sudo updatedb       # Actualizar la DB
```

```bash
❯ find  / -name file.txt 2>/dev/null    # Buscar un archivo desde la raiz
❯ find . -name \*conf\*             # Buscar todos los archivos que contengan de nombre 'conf' en el dir actual 
```

```bash
❯ route -n       # Mirar la tabla de ruteo
```

```bash
❯ netstat -nat                     # Mirar la tabla de ruteo y algunos puertos abiertos

❯ netstat -tupln                   # Mirar las IPs, los puertos, sus estados de proceso y su PID
	❯ kill -9  <PID>              # Matar un proceso especifico colocando el PID 

❯ netstat -antu                    # Mirar los estados de los puertos
	❯ kill -9 $(lsof -i:80 -t)    # Matar un proceso especifico colocando el puerto 
```

```bash
❯ hostname -I        # Muestra la dirección IP
```

```bash 
❯ ip a               # Mirar la IP que tienes asignada 
❯ ifconfig           # Mirar la IP asignada en Linux
```

```bash 
❯ netdiscover -i ens33 -r <IP/24>         # Funciona enviando solicitudes ARP

	# i = Interface 
	# r = range 
```

```bash
❯ arp-scan -I ens33 --localnet --ignoredups       # Hacer un escaneo de la red local, no puedes encontrar mas usuarios despues del router que no pertenecen a tu red

	# Escaneamos la Capa 3 del modelo OSI con el protocolo ARP el cual lo hacer por medio de Broadcast en un segmento de la red, y provee el MAC Address
	# I = i mayuscula; es la interface ens33
	# ignoredups = Ignora IPs duplicadas
	 
	# apt install arp-scan  = Instalar ARP-Scan
```

* [IP-Address-Calculator](https://www.ipaddressguide.com/cidr)

```bash
❯ masscan -p21,22,139,445,80,8080,443 -Pn ❮IP/16❯ --rate=5000    # Es una herramienta como nmap que sirve para aplicar escaneos a nivel de red, pero mas potente y rapida

	# Escanea millones de host por minuto
	# En un simple paquete te descubre todos los puertos abiertos que pueda tener un unico host
```

```bash
❯ exiftool ❮Image.jpg❯                      # Para ver si hay metadatos en un archivo 'imagen, pdf' tambien se pueden encontrar usuarios para usarse en el DC 

❯ steghide info <Image.jpg>                 # Para ver si la imagen tiene contenido oculto
❯ steghide extract -sf <Image.jpg>          # Para ver si la imagen tiene contenido oculto (sf=sourcefile)
```

```bash
❯ fcrackzip -v -u -D -p /usr/share/worlists/rockyou.txt <File.zip>    # Nos ayuda a crackear un archivo zip con Fuerza Bruta

	# v = Verbose
	# u = Unzip
	# D = Dictionary attack
	# p = File to attack 'File.zip'
```

```bash 
❯ md5sum file                      # Obtener el hash del archivo 
```

```bash
❯ wget http://❮IP❯/❮File❯          # Para poder cargar o descargar un archivo especifico desde una IP de atacante
```

```bash
❯ curl http://❮IP❯:PORT            # Para ver si hay contenido 
	# v = Verbosity
```

```bash
❯ echo -n "2b22337f218b2d82dfc3b6f77e7cb8ec" | wc -c           # Nos muestra el numero de caracteres en una fila
❯ cat File | wc -l                                             # Te muestra el numero de palabras en fila que existen en ese archivo (l=ele)
```

```bash 
❯ echo '50 40 73 73 77 30 72 64 40 ...' | xargs | xxd -ps -r   # Nos convierte la cadena de Hex a 

	# xargs = Nos pone la cadena en una sola linea
	# xxd = Nos invierte el proceso del Hex
```

```bash
❯ lsof -i:80              # Para ver que servicio esta ocupando cierto puerto
❯ pwdx 1355887            # Le pasamos el PID del comando anterior 'lsof' y asi podemos ver en que ruta se esta ejecutando ese servicio 
```

```bash
❯ tcpdump -i tun0 icmp -n      # Ponerse en escucha en la interfaz i = Tun0 para trazas ICMP, n = No trazas DNS

❯ nc -nlvp 389                 # Ponerse en escucha por el puerto 389 con Netcat 
```

```bash
❯ git clone https://❮IP❯           # Nos clonamos un repositorio de Github
❯ svn checkout https://❮IP❯        # Para clonar una subcarpeta de Github y en donde dice /tree/master quitarlo de la url y colocar /trunk y el resto de la url
```

```bash 
❯ cat file | xclip -sel clip       # Para copiarnos el output a la Clipboard
```

```bash 
❯ nano file.txt          # Crear, modificar el contenido de un archivo
❯ cat file.txt           # Mirar el contenido de un archivo 
❯ head file.txt          # Muestra las primeras 10 lineas del archivo 
❯ tail file.txt          # Muestra las ultimas 10 lineas del archivo 
❯ grep . file.txt        # Buscar un punto dentro del archivo y muestra el resultado 
❯ grep -R .              # Busca un punto en todos los archivos del directorio y muestra los resultados 
```

```python
❯ 7z l <file.zip>              # Podemos ver el contenido interno del archivo zip, gz, bzip2, etc... (l=ele)
❯ 7z x <file.gz>               # Podemos extraer el contenido interno del archivo zip, gz, bzip2, etc...
❯ tar -xf <file.tar.gz>        # Descomprimimos el archivo tar.gz
❯ gzip -d <file.txt.gz>        # Descomprimir archivos gz
❯ unzip <file.zip>             # Descomprimir un archivo zip 
❯ gunzip <file.gz>             # Para descompirmir archivos gzip
```

```bash
# Para las monturas en Docker serian los siguientes comandos

❯ mount -t cifs //127.0.0.1/myshare /mnt/mounted -o username=null,password=null,domain=,rw # Para crear una montura en un dir especifico de nuestra maquina **'/mnt/mounted'**, lo que modifiques en la montura se hara en la maquina real

	# t = Tipo de montura 'cifs'
	# o = Para que no me pida la passwd
	# rw = Crear la montura con capacidad de lectura y escritura

❯ umount /mnt/mounted        # Para eliminar la montura que esta en un dir especifico
❯ mount | grep home          # Nos muestra desde donde hasta donde esta creada esa montura
```

```bash
❯ cp /bin/bash .             # Copiamos la bash en el dir actual
```

```bash
❯ base64 -w 0 File.sh | xclip -set clip      # Codificamos el contenido de un archivo en base64 y lo copiamos en la clipboard

❯ echo kiufgaiuafgajlfufa98ag676a85g6ga7 | base64 -d > File.sh  # Decodeamos el archivo en base64 y lo colocamos en un archivo que se llamara File.sh
```

```bash 
❯ echo -n 'administrator@test.com' | base64      # Codeamos en base64 

❯ base64 data.zip                                # Codeamos en 'base64' el archivo data.zip, es una formade pasar un archivo desde la maquina victima a la de atacante 

	❯ nvim data.zip                             # Creamos el archivo en la maquina de atacante y agregamos el contenido del comando anterior 

❯ cat data.zip | base64 -d | sponge data.zip     # Decodeamos el contenido de 'data.zip' y con sponge agregamos el contenido decodeado al archivo mismo archivo 'sustitucion de contenido' 

❯ echo -n 'administrator@test.com' swaks         # Automatiza la creacion en base64
```

```bash
❯ echo $PATH                              # Nos damos cuenta que tiene un PATH muy pequeno, por lo que vamos a copiar el nuestro a la maquina victima y asi poder extenderlo

❯ export PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin   # Faltan mas PATH pero es para hacer el ejemplo
```

```bash
❯ chown root:root bash                    # Le asignamos de propietario y grupo 'root' a la bash
❯ chown <USER:USER> -R <DIR_PATH>         # Le cambiamos el propietario a un directorio (R=Forma recursiva)
```

```bash
❯ chmod u+x <FILE>                        # Le cambiamos los permisos a un archivo (r=read, w=write, x=execution, u=user, g=grupo, o=other)
❯ chmod 4755 bash                         # Le cambiamos los privilegios SUID y ahora tendra una 's'
❯ chmod 600 id_rsa                        # Para darle un permiso 600 a la id_rsa   
```

```bash
❯ du -hc <File>                    # Ver el peso del archivo 

❯ go build -ldflags "-s -w"        # Bajar el peso mientras se compila un archivo con 'go'
❯ upx <File>                       # Bajar el peso al archivo para transferirlo a la maquina victima mas rapido
```

```bash 
❯ php -f exploit.php     # Ejecutar un archivo con extension PHP
```

```bash 
❯ https://github.com/pwsafe/pwsafe/releases        # Descargar e instalar la tool en Kali 
❯ dpkg -i passwordsafe-debian12-1.21-amd64.deb

❯ pwsafe          # Iniciar la herramienta 
```