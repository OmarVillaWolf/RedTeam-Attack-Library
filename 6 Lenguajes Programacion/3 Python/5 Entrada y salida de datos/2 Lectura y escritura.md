# Lectura y escritura de archivos 

Tags: #Python3 

La lectura y escritura de archivos son operaciones fundamentales en la mayoría de los programas, y Python proporciona herramientas sencillas y poderosas para manejar archivos. En esta clase, aprenderemos cómo abrir, leer, escribir y cerrar archivos de manera eficiente y segura.

**Manejo Básico de Archivos**
Explicaremos cómo utilizar la función ‘**open()**‘ para crear un objeto archivo y cómo los modos de apertura (‘**r**‘ para lectura, ‘**w**‘ para escritura, ‘**a**‘ para añadir y ‘**b**‘ para modo binario) afectan la manera en que trabajamos con esos archivos.

**Lectura de Archivos**
Detallaremos cómo leer el contenido de un archivo en memoria, ya sea de una sola vez con el método ‘**read()**‘ o línea por línea con ‘**readline()**‘ o iterando sobre el objeto archivo.

**Escritura en Archivos**
Examinaremos cómo escribir en un archivo usando métodos como ‘**write()**‘ o ‘**writelines()**‘, y cómo estos métodos difieren en cuanto a su manejo de strings y secuencias de strings.

**Manejadores de Contexto**
Uno de los aspectos más importantes de la lectura y escritura de archivos en Python es el uso de manejadores de contexto, proporcionados por la declaración ‘**with**‘. Los manejadores de contexto garantizan que los recursos se manejen correctamente, abriendo el archivo y asegurándose de que, sin importar cómo o dónde termine el bloque de código, el archivo siempre se cierre adecuadamente. Esto ayuda a prevenir errores comunes como fugas de recursos o archivos que no se cierran si ocurre una excepción.

El uso de ‘**with open()**‘ no solo mejora la legibilidad del código, sino que también simplifica el manejo de excepciones al trabajar con archivos, haciendo el código más seguro y robusto.

```python 
f = open("example.txt", "w")         # Abrimos un archivo
	# w = 'write' y sobreescribe el contenido de el archivo por lo que no agrega nuevo contenido sino que lo borra y lo escribe 
	# r = 'read' y sirve para leer el archivo 
	# a = 'append' y sirve para agregar algo hasta abajo de todo el archivo 
	# rb = raw-binary
```

```python 
#!/usr/bin/env python3 

# Crearemos un archivo example.txt ("Hola Mundo")

f = open("example.txt", "w")        # Abrimos un archivo 
f.write("Hola Mundo\n")             # Si el archivo no existe lo crea y le agrega el contenido 
f.write("Hasta luego!")

f.close()                           # Sirve para cerrar el archivo al finalizar las acciones 
```

```python 
#!/usr/bin/env python3

with open("example.txt", "w") as f:     # Si se hace de esta manera, Python 'automaticamente' cierra el archivo 
	f.write("Hola Mundo\n")
```

```python 
#!/usr/bin/env python3

with open("/etc/hosts", "r") as f:
	file_content = f.read()            # Obtenemos de modo textual en una cadena el contenido del archivo 

print(file_content)
```

```python 
#!/usr/bin/env python3

with open("/etc/hosts", "r") as f:
	first_line = f.readline()           # Leer la primer linea  

print(first_line)
```

```python 
#!/usr/bin/env python3

with open("/etc/hosts", "r") as f:      # Forma mas optima de leer y ver un arhivo 
	for line in f:                     # Leer linea por linea  
		print(line.strip())           # Con la funcion 'strip' puedes quitar el salto de linea en cada ciclo  
```

```python 
#!/usr/bin/env python3

with open("/usr/share/wordlist/rockyou.txt", "rb") as f:     # Forma no optima de leer y ver un arhivo 
	for line in f.readlines():                     
		print(line.strip().decode())                       # La funcion 'decode' convierte el formato binary a string
```

```python 
#!/usr/bin/env python3

mi_lista = ["Primera linea\n", "Segunda linea\n", "Tercera linea\n", "Cuarta linea"]

with open("example.txt", "w") as f:
	f.writelines(mi_lista)
```

```python 
#!/usr/bin/env python3

# Comprobar si una archivo existe o no 
try:     
	with open("example.txt", "r") as f:
		print(f.read())
except FileNotFoundError:
	print("\n[!] No ha sido posible encontrar este archvo")
```

## Copiar el contenido de una imagen 

```python 
# Si los archivos no son legibles, debemos de considerarlos como binarios

#!/usr/bin/env python3

# Copiar el contenido de una imagen 'f_in = file input' en otro archivo 'f_out = file out'
with open("fondo.png", "rb") as f_in, open("img.png", "wb") as f_out: 
	file_content = f_in.read()        # Leera y guardara el contenido bruto del binario 
	f_out.write(file_content)         # Todo el contenido del 'f_in' lo escribiras en 'f_out'
```