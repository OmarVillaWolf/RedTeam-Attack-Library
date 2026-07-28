# Reconocimiento 

Tags: #PrivEsc 

## Reconocimiento general

```bash 
1. Mirar archivos en '/home' que no sean muy normales que esten ahí
2. Mirar archivos de 'config' en rutas especificas 
3. Mirar el 'bash_history'

❯ whoami             # Mirar el usuario 
❯ id                 # Mirar los grupos 
❯ ipconfig | ip a    # mirar las interfaces 
❯ hostaname          # Muestra el nombre del host
❯ sudo su            # A veces el usuario no necesita password para ingresar 
❯ cat /etc/issue         # Identificar la distribución actual del SO

❯ cat /etc/*release      # Identificar la versión del Debian que se esta ejecutando 
❯ cat /etc/os-release

❯ echo $PATH         # Mirar el PATH del usuario
❯ env                # Muestra las variables del sistema (A veces hay contraseñas) 

❯ uname -a           # Muestra información como hostname, Kernel, arquitectura 
❯ uname -r           # Muestra solo la versión del Kernel

❯ lsb_release -a     # Mirar la versión de Linux 

❯ netstat -nat       # Mirar los puertos abiertos 

❯ lscpu                # Muestra la información del CPU

❯ free -h
❯ df -h                # Lista los disco duros que hay en el sistema y su información  
❯ df -ht ext4          # Muestra información del disco '/dev/sda'
❯ lsblk | grep sd      # Sistema de archivos y unidades adicionales 
❯ cat /etc/fstab | grep -v "#" | column -t    # Listar los sistemas de archivos NO montados 

❯ dpkg -l              # Muestra los paquetes instalados 
```

## Grupos / Usuarios 

```bash 
❯ whoami               # Muestra el nombre del usuario actual 

❯ cat /etc/group       # Muestra los grupos y usuarios asignados
❯ getent group sudo    # Lista miembro de cualquier grupo interesante 

❯ groups root          # Muestra al grupo que pertenece un usuario en específico 
❯ groups               # Listas los grupos del SO

❯ cat /etc/shells      # Muestra las shells de inicio de sesión 

❯ cat /etc/passwd      # Mirar los usuarios existentes 
❯ cat /etc/passwd | cut -f1 -d:
❯ cat /etc/passwd | grep -v /nologin    # v = Excluir

❯ ls /home 
❯ ls -la /home/user/      # Mirar el contenido del directorio home de un usuario  
❯ cat ~/.bash_history     # Mirar su historial de comandos 
❯ history                 # Mirar el historial de Bash 

❯ find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep student1   # Listar todos los archivos ocultos del usuario
❯ find / -type d -name ".*" -ls 2>/dev/null     # Listar todos los directorios ocultos 
❯ ls -l /tmp /var/tmp /dev/shm                  # Listar los archivos temporales 


❯ ls -l ~/.ssh            # Mirar el contenido del directorio SSH

❯ last                    # Muestra los últimos usuarios loggeados en el SO 
❯ lastlog                 # Muestra los usuarios loggeados en el SO
```

## Network 

```bash 
❯ ifconfig                  # Muestra las interfaces y su IP
❯ ip a s                    # Muestra las interfaces y los adaptadores 
 
❯ cat /etc/networks         # Lista las interfaces y su configuración 
❯ cat /etc/hostname         # Muestra el nombre del host 
❯ cat /etc/hosts            # Muestra todos los host y su dominio pertinente 
❯ cat /ect/resolv.conf      # Muestra el nombre y su IP del servidor DNS 

❯ arp -a                    # Mirar la tabla ARP 
❯ route                     # Mirar la tabla de enrutamiento 
```