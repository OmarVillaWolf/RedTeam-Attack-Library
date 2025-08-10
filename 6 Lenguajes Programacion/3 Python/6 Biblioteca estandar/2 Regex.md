# Regex 'Expresiones Regulares'

Tags: #Python3 #Regex 

La librería ‘**re**‘ en Python proporciona un conjunto completo de herramientas para trabajar con expresiones regulares, que son patrones de cadenas diseñados para la búsqueda y manipulación de texto.

Aquí hay varios aspectos importantes de esta librería:

- **Funciones Básicas**: ‘**re**‘ incluye funciones como ‘**search**‘ (para buscar un patrón dentro de una cadena), ‘**match**‘ (para verificar si una cadena comienza con un patrón específico), ‘**findall**‘ (para encontrar todas las ocurrencias de un patrón), y ‘**sub**‘ (para reemplazar partes de una cadena que coinciden con un patrón).
- **Compilación de Patrones**: Permite compilar expresiones regulares en objetos de patrón, lo que puede mejorar el rendimiento cuando se usan repetidamente.
- **Grupos y Captura**: Ofrece la capacidad de definir grupos dentro de patrones de expresiones regulares, lo que facilita extraer partes específicas de una cadena que coinciden con subpatrones.
- **Flags**: Soporta modificadores que alteran la forma en que las expresiones regulares son interpretadas y coincididas, como ignorar mayúsculas y minúsculas o permitir el modo multilínea.
- **Patrones Complejos**: Permite la creación de patrones complejos utilizando una variedad de símbolos y secuencias especiales, como cuantificadores, aserciones y clases de caracteres.
- **Aplicaciones Prácticas**: Las expresiones regulares son extremadamente útiles en tareas como la validación de formatos (por ejemplo, direcciones de correo electrónico), el análisis de registros (logs), el procesamiento de lenguaje natural, y la limpieza y preparación de datos.
- **Curva de Aprendizaje**: Aunque potentes, las expresiones regulares pueden ser complejas y requieren una curva de aprendizaje. Sin embargo, una vez dominadas, se convierten en una herramienta invaluable en el arsenal de cualquier programador.

```bash 
❯ https://regex101.com/     # Practicar las Regex
❯ https://cheatography.com/davechild/cheat-sheets/regular-expressions/   # Cheat Sheet
```

```python 
#!/usr/bin/env python3 

import re

text = "Mi gato esta en el tejado y mi otro gato esta en el jardin"

matches = re.findall("gato", text)  # Encuentra la palabra 'gato' en el texto y nos regresa todas las coincidencias
print(matches)
```

```python 
#!/usr/bin/env python3 

import re

text = "Hoy estamos a 25/08/2024, mañana estaremnos a 26/08/2024"

matches = re.findall("\d{2}\/\d{2}\/\d{4}", text)    # Nos devolvera las dos fechas 
	
	# \d{} = Indicas el numero de digitos 
	# \/ = Indicamos que hay una barra '/' pero debemos escaparla

print(matches)
```

```python 
#!/usr/bin/env python3 

import re

text = "Los usuarios pueden contactar a soporte@omar.io o a info@omar.io"

matches = re.findall("(\w+)@(\w+\.\w{2,})", text)  # Nos devolvera una lista de 'tuplas'
	
	# \w = Contempla un valor alfanumericos
	# + = Indicas que hay mas caracteres del tipo anterior '\w'
	# \w{2,} = Indicas que hay caracteres alfanumericos con longitud minima de '2' hasta lo que sea...
	# \. = Indicar un existe un punto '.' pero debemos escaparlo

print(matches)
```

```python 
#!/usr/bin/env python3 

import re

text = "Mi gato esta en el tejado y mi otro perro esta en el jardin"

matches = re.sub("gato", "perro", text)       # Sustituyes la primer palabra por la segunda

print(matches)
```

```python 
#!/usr/bin/env python3 

import re

text = "Campo1,Campo2,Campo3,Campo4,Campo5"

matches = re.split(",", text)       # Separar por una coma ',' los diferentes campos

print(matches)
```

```python 
#!/usr/bin/env python3 

import re

def validar_correo(correo):
	patron = r"\b[A-Za-z0-9._+-]+@[A-Za-z0-9]+\.[A-Za-z]{2,}\b"

	# r = Sirve para interpretar caracteres especiales como: '\n, \b, etc...'
	# \b = Sirve para indicar que la cadena debe de empezar o terminar con... 
	# [A-Za-z0-9._+-] = Indicas que pueden existir todos esos caracteres 
	# + = Indicar que existen mas caracteres del tipo anterior 
	# \. = Indicas que existe un punto '.' pero lo escapamos
	# [A-Za-z]{2,} = Indicas que existen esos caracteres con longitud minima de '2' hasta lo que sea...

	if re.findall(patron, correo):    # Si el patron coincide con el correo devuelve la coincidencia
		return True
	else:
		return False

print(validar_correo("soporte@omar.io"))
```

```python 
#!/usr/bin/env python3 

import re

text = "Hoy estamos a 25/08/2024, mañana estaremnos a 26/08/2024"
patron = r"\b(\d{2}\/\d{2}\/\d{4})\b"    # Nos devolvera las dos fechas 

	# r = Sirve para interpretar caracteres especiales como: '\n, \b, etc...'
	# \b = Sirve para indicar que la cadena debe de empezar o terminar con... 
	# \d{} = Indicas el numero de digitos 
	# \/ = Indicamos que hay una barra '/' pero debemos escaparla

for match in re.finditer(patron, text):
	print(f"La fecha es: {match.group(0)}, la cual empieza en la posicion {match.start()} y termina en {match.end()}")
```
