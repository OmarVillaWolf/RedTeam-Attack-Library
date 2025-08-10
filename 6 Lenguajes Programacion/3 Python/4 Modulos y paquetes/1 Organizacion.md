# Organización del código en módulos 

Tags: #Python3 

La organización del código en módulos es una práctica esencial en Python para construir programas escalables y mantenibles. Los módulos son archivos de Python que contienen definiciones y declaraciones de variables, funciones, clases u otros objetos que se pueden reutilizar en diferentes partes del programa.

**Estructura de Módulos**
Cada módulo en Python es un archivo ‘**.py**‘ que encapsula tu código para un propósito específico. Por ejemplo, puedes tener un módulo para operaciones matemáticas, otro para manejo de entradas/salidas, y otro para la lógica de la interfaz de usuario. Esta estructura ayuda a mantener el código organizado, facilita la lectura y hace posible la reutilización de código.

**Importación de Módulos**
Python utiliza la palabra clave ‘**import**‘ para utilizar módulos. Puedes importar un módulo completo, como ‘**import math**‘, o importar nombres específicos de un módulo utilizando ‘**from math import sqrt**‘. Python también permite la importación de módulos con un alias para facilitar su uso dentro del código, como ‘**import numpy as np**‘.

**Paquetes**
Cuando los programas crecen y los módulos comienzan a acumularse, Python permite organizar módulos en paquetes. Un paquete es una carpeta que contiene módulos y un archivo especial llamado ‘**__init__.py**‘, que indica a Python que esa carpeta contiene módulos que pueden ser importados.

**Ventajas de los Módulos**
- **Mantenimiento**: Los módulos permiten trabajar en partes del código de manera independiente sin afectar otras partes del sistema.
- **Espacio de Nombres**: Los módulos definen su propio espacio de nombres, lo que significa que puedes tener funciones o clases con el mismo nombre en diferentes módulos sin conflicto.
- **Reutilización**: El código escrito en módulos puede ser reutilizado en diferentes programas simplemente importándolos donde se necesiten.

## Creación de un módulo 

```python
1. Creamos un script el cual llamaremos 'math_operations.py', este sera nuestro modulo que sera utilizado despues 

#!/usr/bin/env python3 

def suma(x, y):
	return x + y 

def rest(x, y):
	return x - y

def multiplicacion(x, y):
	return x * y

def division(x, y):
	if y == o:
		raise ValueError("[!] No es posible dividir entre cero")
	else:
		return x / y
```

```python 
2. Forma de utilizar un modulo 

#!/usr/bin/env python3 

import math_operations                # Importamos el modulo 

print(math_operations.suma(4, 9))     # Llamamos a la funcion del modulo 
```

```python 
3. Otra forma de importar el modulo 

#!/usr/bin/env python3 

from math_operations import suma, resta, multiplicacion , division     # Importas funciones especificas del modulo 

print(suma(4, 9))
```

## Módulos

```python 
#!/usr/bin/env python3 

import math 

print(dir(math))      # Muestra las propiedades asi como las funciones del modulo 
```

```python 
#!/usr/bin/env python3

from math import sqrt          # Importar una funcion especifica
from math import *             # Importamos todas las funciones del modulo 

print(int(sqrt(16)))           # Muestra la raiz cuadrada del numero 16
```