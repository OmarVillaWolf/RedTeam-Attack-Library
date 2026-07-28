# Reconocimiento 

Tags: #PrivEsc 

## Reconocimiento general

```bash 
1. Mirar archivos en '/home' que no sean muy normales que esten ahí
2. Mirar archivos de 'config' en rutas especificas 
3. Mirar el 'bash_history'

❯ whoami             # Mirar el usuario 

❯ ipconfig           # mirar las interfaces 

❯ id                 # Mirar los grupos 

❯ lsb_release -a     # Mirar la versión de Linux 

❯ netstat -nat       # Mirar los puertos abiertos 

❯ hostaname               # Muestra el nombre del host

❯ ls /home                # Contenido del directorio home 

❯ cat /etc/issue          # Identificar la distribucion actual del SO
❯ cat /etc/*release       # Identificar la version del Debian que se esta ejecutando 

❯ cat /etc/passwd         # Miraar el archivo 'passwd' muestra los usuarios '/bin/bash'

❯ uname -a                # Muestra informacion como hostname, Kernel, arquitectura 
❯ uname -r                # Muestra solo la version del Kernel

❯ env                     # Muestra las variables del sistema 

❯ lscpu                   # Muestra la informacion del CPU

❯ free -h
❯ df -h                   # Lista los disco duros que hay en el sistema y su informacion  
❯ df -ht ext4             # Muestra informacion del disco '/dev/sda'
❯ lsblk | grep sd         # Sistema de archivos y unidades adicionales 

❯ dpkg -l                 # Muestra los paquetes instalados 
```

## Grupos / Usuarios 

```bash 
❯ whoami                  # Muestra el nombre del usuario actual 

❯ groups root             # Muestra al grupo que pertenece un usuario en especifico 
❯ groups                  # Listas los grupos del SO

❯ cat /etc/passwd         # Muestra los usuarios en el SO y tienen un '/bin/bash'
❯ cat /etc/passwd | grep -v /nologin    # v = Excluir

❯ ls -la /home/user/      # Mirar el contenido del directorio home de un usuario  
❯ cat ~/.bash_history     # Mirar su historial de comandos 
❯ history                 # Mirar el historial de Bash 

❯ ls -l ~/.ssh            # Mirar el contenido del directorio SSH
❯ 

❯ last                    # Muestra los ultimos usuarios loggeados en el SO 
❯ lastlog                 # Muestra los usuarios loggeados en el SO
```

## Network 

```bash 
❯ ifconfig                  # Muestra las interfaces y su IP
❯ ip a s                    # Muestra las interfaces y los adaptadores 
 
❯ cat /etc/networks         # Lista las interfaces y su configuracion 
❯ cat /etc/hostname         # Muestra el nombre del host 
❯ cat /etc/hosts            # Muestra todos los host y su dominio pertinente 
❯ cat /ect/resolv.conf      # Muestra el nombre y su IP del servidor DNS 

❯ arp -a                    
```

## Reconocimiento específico 

```bash 
❯ sudo -l                          # Sudoers  
❯ find \-perm -4000 2>/dev/null    # Binarios SUID
❯ systemctl list-timers            # Mirar si hay tareas a punto de ejecutarse 
❯ cat /etc/crontab                 # Mirar tareas Cron 
❯ getcap -r / 2>/dev/null          # Capabilities 
❯ ps -faux                         # Listar procesos 
❯ ps -eo user,command              # Mirar comandos en tiempo real 
❯ ss -nltp                 # Mirar puertos abiertos (Usar SSH, Ligolo, Chisel para traerlos a Kali)


# Si se encuentra un archivo ejecutable 
❯ strings file.sh                  # Mirar las cadenas de caracteres imprimibles  
	❯ strings -e l file.sh        # Mirar cadenas adicionales 
```