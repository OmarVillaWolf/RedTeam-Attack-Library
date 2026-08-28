# Movimiento lateral — MySQL

Tags: #PrivEsc #MovimientoLateral 

## Identificación de la base de datos

```bash 
# Linux — rutas comunes 

/var/www/html/config.php
/var/www/html/config.inc.php
/var/www/html/configuration.php
/var/www/html/db.php
/var/www/html/database.php
/var/www/html/.env

/var/www/<APP>/config.php
/var/www/<APP>/config/db.php
/var/www/<APP>/config/database.php
/var/www/<APP>/.env

/opt/<APP>/config/
/opt/<APP>/config/db.php
/opt/<APP>/.env

/home/<USER>/<APP>/.env
/home/<USER>/<APP>/config.php
```
### Acceso a MySQL

```bash
mysql -h <IP> -u <usuario> -p 

	❯ show databases;      # Mostrar todas las bases de datos existentes
	❯ use ❮DB_name❯;       # Usar una base de datos especifica
	❯ show tables;         # Mostrar el contenido de las tablas de la DB elegida
	❯ select * from ❮Table_name❯;     # Dumpear toda la info de la tabla users, incluyendo sus hashes
```