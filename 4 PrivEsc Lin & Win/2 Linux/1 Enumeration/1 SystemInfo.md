# Enumeración Local en Linux 

Tags: #LocalEnumeration #Linux #PrivEsc #Meterpreter 

## Enumeración de la información del sistema 

```bash 
# Que estamos buscando?

1. Hostname 
2. Versión de distribución 
3. Versión de Kernel y arquitectura
4. Infromación de CPU
5. Información del disco duro, y dispositivos de montura 
6. Software y paquetes instalados 
```

## Comandos Linux

```bash 
# Si al cambiar de Meterpreter a Shell no te muestra una interfaz, debes de colocar '/bin/bash -i' 

❯ hostaname                 # Muestra el nombre del host

❯ cat /etc/issue            # Identificar la distribucion actual del SO
❯ cat /etc/*release         # Identificar la version del Debian que se esta ejecutando 

❯ uname -a                  # Muestra informacion como hostname, Kernel, arquitectura 
❯ uname -r                  # Muestra solo la version del Kernel

❯ env                       # Muestra las variables del sistema 

❯ lscpu                     # Muestra la informacion del CPU

❯ free -h
❯ df -h                     # Lista los disco duros que hay en el sistema y su informacion  
❯ df -ht ext4               # Muestra informacion del disco '/dev/sda'
❯ lsblk | grep sd

❯ dpkg -l                   # Muestra los paquetes instalados 
```

## Con Meterpreter 

```bash 
❯ sysinfo              # Muestra la informacion del sistema, IP, SO, Arquitectura 
❯ shell                # Cambias a uns Shell de Linux 
```