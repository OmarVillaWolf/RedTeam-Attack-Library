# MYSQL 

Tags: #MySQL #Servidor #Comandos #DB 

## DATABASE - TABLES - COLUMS - DATA 

```bash
❯ sudo apt install mariadb-server              # Instalar Mariadb
```

```bash
❯ service mariadb start                        # Iniciar el servicio
❯ service mariadb stop                         # Parar el servicio
```

```bash 
❯ mysql -u root -p passwd -D database

❯ mysql -u root -p -h <IP>         # Fuera del server 
❯ mysql -u root -p                 # Dentro del server 

	# h = Host
	# u = User
	# p = passwd    -> Dar enter, root, admin -> Passwd por defecto
	# D = Especificar una base de datos para conectarse 
```

```bash
❯ mysql -u root -p          # Conectar y proporcionar credenciales

	❯ show databases;       # Mostrar todas las bases de datos existentes
	❯ use ❮DB_name❯;        # Usar una base de datos especifica
	❯ show tables;          # Mostrar el contenido de las tablas de la DB elegida
	❯ select * from ❮Table_name❯;    # Dumpear toda la info de la tabla users, incluyendo sus hashes
	❯ describe ❮Table_name❯;         # Mirar que columnas existen
	❯ select count(*) from ❮Table_name❯        # Mirar los registros de la tabla de la DB seleccionada
	❯ select User,Password from ❮Table_name❯;  # Seleccionar los campos de una tabla especifica 
	
	❯ select * from ❮Table_name❯ where username=’admin’;
	❯ select load_file("/etc/shadow");    # Si hay permisos se puede ver el archivo '/etc/shadow'
```

```bash 
❯ php -r '$m=new mysqli("mysql","root","trinity","nexust_joomla"); if($m->connect_error){die($m->connect_error);} $r=$m->query("SHOW TABLES"); while($row=$r->fetch_row()){echo $row[0].PHP_EOL;}'
```

## Crear una DB en MSQL

```bash 
	❯ create database ❮DB_name❯;          # Crear una base de datos
	
	# Crear una tabla llamada 'Users', crear columnas con sus nombres 'username, password,subscription' e indicar su tipo de dato 'varchar(32)'
	❯ create table users (id int auto_increment PRIMARY KEY, username varchar(32), password varchar(32), subscription varchar(32));            
	
	# Insertar en la tabla llamada users los valores para llenarla
	❯ insert into users(username, password, subscription) values(“admin”,“admin123”,“no aplica”) 
	
	❯ drop table ❮Table_name❯;            # Eliminar la tabla llamada users
	❯ drop database ❮DB_name❯;            # Eliminar la DB 
```
