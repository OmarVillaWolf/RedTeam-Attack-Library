# Windows Commands

Tags: #Windows #Comandos 

## Comandos terminal Windows

```bash 
❯ del /s /q "file"                     # Eliminar un archivo 
❯ rmdir /s /q "C:/dir/"                # Eliminar una carpeta 
```

```bash
❯ ping -n 1 ❮IP❯                       # Para saber si la maquina esta activa o no (ttl=64 Linux, ttl=128 Windows)

	# IP = IP Address de la maquina target 
	# n = Numero de pings a ejecutar
```

```bash 
❯ arp -a                               # Mirar la tabla ARP en la maquina actual 
```
ty
```bash 
❯ route add IP mask 255.255.255.255 GW   # Agregar una IP en especifico
	# IP = Direccion a agregar
	# GW = Direccion IP del Gateway

❯ route add IP mask 255.255.255.0 GW     # Agregar un segmento de red 

❯ route delete IP                        # Borrar una IP especifica o un segmento de red
```

## Enumeración 

```bash
# Historial en Windows
❯ type AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt   

Notas:
	1. El comando se ejecuta en el dir del usuario 'C:\Users\omar'
```

```bash
❯ ipconfig                   # Muestrar interfaces, direcciones IP y si existen mas subredes hacer 'Pivoting'
```

```bash
❯ dir                      # Lista el contenido del directorio
❯ dir C:\Users             # Lista los directorios de los usuarios 

❯ dir /r /s File.txt       # Busca de forma recursiva el archivo '.txt'
❯ dir /r /s                # Listar archivos con ADS 'Alternative Data Streams' ocultos 

❯ dir -Force               # Lista todos los archivos hasta los ocultos
```

```bash
❯ type ❮File❯              # Mostrar el contenido del archivo

❯ more < file.txt          # Mirar el contenido del archivo en partes 
```

```bash 
❯ systeminfo               # Mostrar la informacion de Windows (Parches 'Hotfix', etc...)
❯ sysinfo                  # Mostrar algunos detalles de Windows
```

```bash
❯ whoami                   # Mirar el nombre del usuario
❯ whoami /priv             # Mirar los privilegios 'Token' del usuario 
❯ whoami /all              # Mirar toda la info del usuario 
```

```bash 
❯ query user               # Mirar si hay algun usuario loggeado
```

```bash
❯ net user                             # Mirar todos los usuarios existentes y sus grupos de forma local
❯ net user <User>                      # Mirar los grupos de un usuario especifico como 'administrator' de forma local
❯ net user admin password123           # Cambiar la passwd al usuario admin siendo NT Authority\System de forma local  
❯ net user admin password123 /domain   # Cambiar la passwd a un usuario admin siendo un usuario que pertenece a un grupo con privilegios para hacerlo en el dominio 

❯ net group "Group"                    # Mirar los grupos del usuario 
❯ net localgroup "administrators"      # Mirar los miembros del grupo administrador de forma local


❯ net user omar P4ssw0rd /add               # Crear un usuario siendo NT Authority\System de forma local
❯ net localgroup Administrators omar /add   # Agregar al usuario al grupo local 'Administrators'
```

```bash 
❯ hostname             # Mirar el nombre de la maquina 
```

```bash
❯ .\SharpHound.exe     # Ejecutar un archivo .exe en Powershell o CMD
```

```bash 
❯ cd PROGRA~1          # Colocar las 6 primeras letras e ingresar a un dir especifico con espacios 'Program Files'

Notas:
	1. Colocar '~2' si se quiere ingresar a otro dir aunque se tenga las primeras 6 letras iguales
```

```bash
❯ cls       # Limpiar la pantalla
```

