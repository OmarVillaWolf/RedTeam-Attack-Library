# Bypass con null byte y validación de extensión

Tags: #DirectoryPathTraversal #BurpSuite 

```bash 
En este lab la aplicación intenta asegurar que los archivos solicitados terminen en .png, pero no considera el uso del byte nulo ('%00').

Este carácter indica el final de cadena para muchas funciones en lenguajes como C. Al enviar 'https://web.com/../etc/passwd%00.png', el servidor interpreta solo lo anterior al '%00', accediendo así a '/etc/passwd' a pesar de que la validación de extensión aparentemente se cumple.
```

```bash 
	?file=/var/www/images/../../../etc/passwd%00.png     # Mirar un archivo en específico

	# %00 = El Byte null sirve para aislar o separar la extensión 'png' y no sea interpretada


Notas:
	 1. Ya no funciona en versiones => 5.3.4 de PHP 
```