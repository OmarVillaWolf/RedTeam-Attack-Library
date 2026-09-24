# Movimiento lateral — MySQL

Tags: #PrivEsc #MovimientoLateral 

## Mysql credentials 
```bash 
	/var/www/html/config.php
	/var/www/html/db.php
	/var/www/html/database.php
	/var/www/html/wp-config.php
	/var/www/html/configuration.php
	/var/www/config.php
	/var/www/config/db.php
	/var/www/config/database.php
	/opt/config/db.php
	/srv/config/db.php
	/etc/mysql/my.cnf
	/etc/phpmyadmin/config.inc.php
```
### Acceso a MySQL

```bash
mysql -h <IP> -u <usuario> -p 

	❯ show databases;      # Mostrar todas las bases de datos existentes
	❯ use ❮DB_name❯;       # Usar una base de datos especifica
	❯ show tables;         # Mostrar el contenido de las tablas de la DB elegida
	❯ select * from ❮Table_name❯;     # Dumpear toda la info de la tabla users, incluyendo sus hashes
```