# Entrada por teclado y salida por pantalla 

Tags: #Python3 

La interacción del usuario a través de la consola es una habilidad esencial en Python, y en esta clase, exploraremos las funciones que permiten la entrada y salida de datos. Utilizaremos ‘**input()**‘ para recoger la entrada del teclado y ‘**print()**‘ para mostrar mensajes en la pantalla.

En cuanto al formato de texto, veremos cómo manejar y presentar la información de manera amigable. Esto incluye desde la manipulación básica de cadenas hasta técnicas más avanzadas de formateo de cadenas que permiten la inserción de variables y la alineación del texto.

La codificación de caracteres es un aspecto clave para garantizar que la entrada y salida manejen adecuadamente diferentes idiomas y conjuntos de caracteres, preservando la integridad de los datos y la claridad de la comunicación en la consola.

```python
#!/usr/bin/env python3 

nombre = input("\n[+] Dime tu nombre: ")

print(f"\n[+] Perfecto, ahora se que te llamas {nombre}")
```

```python 
#!/usr/bin/env python3 

while True:
	try:
		edad = int(input("\n[+] Dime tu edad: "))
		print(f"\n[+] Perfecto, el siguiente año deberias cumplir {edad+1} años")
		break
	except ValueError:
		print(f"\n[!] El tipo de dato es incorrecto")
```

```python 
#!/usr/bin/env python3 

from getpass import getpass          # Importamos el modulo 'getpass' con una funcion llamada 'getpass'

password = getpass("\n[+] Introduce tu password: ")   # Al introducir la password esta no se podra ver por 'getpass'
print(f"\n[+] Tu password es: {password}")
```
