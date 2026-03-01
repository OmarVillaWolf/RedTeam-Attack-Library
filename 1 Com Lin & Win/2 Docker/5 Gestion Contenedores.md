# Comandos comunes para la gestión de contenedores

Tags: #Docker #Comandos #Linux #DockerCompose

A continuación, se detallan algunos de los comandos:

```bash
❯ apt remove docker-compose               # Eliminar Docker-Compose
❯ curl -L "https://github.com/docker/compose/releases/download/v5.0.1/docker-compose-$(uname -s)-$(uname -m)" -o docker-compose  # Descarga un binario de Docker-Compose
❯ chmod +x docker-compose                 # Le damos permisos de ejecucion
❯ cp docker-compose /usr/libexec/docker/cli-plugins   # Para que funcione al instalar Bloodhound CE
❯ mv docker-compose /usr/local/bin        # Movemos el binario a esa ruta para que se ejecute desde cualquier parte 
```

```bash
-docker rm $(docker ps -a -q) --force: # Este comando se utiliza para eliminar todos los contenedores en el sistema, incluyendo los contenedores detenidos. La opción “-q” se utiliza para mostrar sólo los identificadores numéricos de los contenedores, y la opción “–force” se utiliza para forzar la eliminación de los contenedores que están en ejecución. Es importante tener en cuenta que la eliminación de todos los contenedores en el sistema puede ser peligrosa, ya que puede borrar accidentalmente contenedores importantes o datos importantes. Por lo tanto, se recomienda tener precaución al utilizar este comando.

-docker rm id_contenedor: # Este comando se utiliza para eliminar un contenedor específico a partir de su identificador. Es importante tener en cuenta que la eliminación de un contenedor eliminará también cualquier cambio que se haya realizado dentro del contenedor, como la instalación de paquetes o la modificación de archivos.

-docker rmi $(docker images -q): # Este comando se utiliza para eliminar todas las imágenes de Docker en el sistema. La opción “-q” se utiliza para mostrar sólo los identificadores numéricos de las imágenes. Es importante tener en cuenta que la eliminación de todas las imágenes de Docker en el sistema puede ser peligrosa, ya que puede borrar accidentalmente imágenes importantes o datos importantes. Por lo tanto, se recomienda tener precaución al utilizar este comando.

-docker rmi id_imagen: # Este comando se utiliza para eliminar una imagen específica a partir de su identificador. Es importante tener en cuenta que la eliminación de una imagen eliminará también cualquier contenedor que se haya creado a partir de esa imagen. Si se desea eliminar una imagen que tiene contenedores en ejecución, se deben detener primero los contenedores y luego eliminar la imagen.
```

```bash
❯ docker stop ❮ID❯               # Parar el contenedor que se esta ejecutando y solo debemos de poner su identificador 
❯ docker rm ❮ID❯ --force         # Borrar un contenedor dado con su ID y lo hacemos de manera forzada 
❯ docker ps -a -q                # Devolver el identificador de todos los contenedores y asi lo podriamos usar para poder eliminarlos completamente todos de un jalon
❯ docker rm $(docker ps -a -q) --force                      # Asi borras el contenedor de Docker
```

```bash
❯ docker rmi ❮ID❯                               # Borrar una imagen en especifico
❯ docker rmi $(docker images -q)                # Borrar todas las imagenes con su identificador
```

```bash
❯ docker volume ls                               # Listar los volumenes creados 
❯ docker volume rm $(docker volume ls -q)        # Borrar todos los volumenes con su nombre
```

```bash 
❯ docker network ls                                         # Listar las interfaces de Docker 
❯ docker network rm ❮ID❯                                    # Borrar la interfaz de Docker 
❯ docker network rm $(docker network ls -q)                 # Borrar todas las interfaces de Docker, no borrara las interfaces 'red bridge' que vienen por defecto
```