# SQLite3 

Tags: #Tool #SQLite 

```bash 
❯ sqlite3 file.db        # Mirar la DB con SQLite3

	❯ .schema user           # Estructura de la DB 
	❯ .tables                # Mirar las tablas 
	❯ select * from user;    # Mirar el contenido de una tabla en específico 
	❯ .quit                  # Salir de la DB 
```

```bash 
# Forma más directa 
❯ sqlite3 file.db ".schema user"   # Muestra la estrcutura de la DB
❯ sqlite3 file.db ".tables"        # Mirar las tablas 
❯ sqlite3 file.db "SELECT * FROM user LIMIT 1;"  # Muestra los datos de una table en específico 'user'
```

```bash 
# Forma bonita 
❯ sqlite3 -header -column file.db "SELECT login, password, salt, rands FROM user;"
```

## Tool Gráfica 

```
❯ sqlite    # Herramienta gráfica para cargar las DB
```