# Detectar inyecciones SQL 

Tags: #Hacking #SQLI #MSSQL #DSSS #OwaspZap  

## DSSS 

```bash 
# Kali Tool para mostrar que parametro es vulnerable a una injección SQL

Nota:
	1. Se necesita estar dentro con credenciales validas para asi obtener la cookie 
	2. Buscar el campo donde se encuentre el 'id=1' 
	3. Ir al 'Inspector', dar click en 'Console' y colocar '>> document.cookie'

❯ python3 dsss.py -u "http://domain.com/file.aspx?id=1" --cookie="cookie_value"
```