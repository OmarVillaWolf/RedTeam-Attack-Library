# String Formating 

Tags: #Python3 

Python proporciona varias maneras de formatear cadenas, permitiendo insertar variables en ellas, así como controlar el espaciado, alineación y precisión de los datos mostrados. Aquí están las técnicas de formateo de cadenas que exploraremos:

**Operador % (Porcentaje)**
También conocido como “i**nterpolación de cadenas**“, este método clásico utiliza marcadores de posición como ‘**%s**‘ para cadenas, ‘**%d**‘ para enteros, o ‘**%f**‘ para números de punto flotante.

**Método format()**
Introducido en Python 2.6, permite una mayor flexibilidad y claridad. Utiliza llaves ‘**{}**‘ como marcadores de posición dentro de la cadena y puede incluir detalles sobre el formato de la salida.

**F-Strings (Literal String Interpolation)**
Disponible desde Python 3.6, los F-Strings ofrecen una forma concisa y legible de incrustar expresiones dentro de literales de cadena usando la letra ‘**f**‘ antes de las comillas de apertura y llaves para indicar dónde se insertarán las variables o expresiones.


```python 
#!/usr/bin/env python3

# Primero se indica el tipo de dato = (%s = String), (%d = decimal)

name = "Omar"
rol = "Ingeniero"
edad = 29

print("Hola, mi nombre es %s y soy %s. Actualmente tengo %d años" % (name, rol, edad))           # Le decimos que queremos sustituir un string '%s' por una cadena la cual se llama 'name', 'rol' y 'edad'


# Otra forma de hacerlo es:
print("Hola, soy {}! y tengo {} años".format(name, edad))                                 # Entre llaves hara la sustitucion del valor de la variable 
print("Hola, soy {0}! y tengo {1} años. Mi nombre real es {0}".format(name, edad))        # Podemos colocar la misma variable en la posicion 0 para ser sustituida en ambos


# Ultima forma de hacerlo es con las f-strings:
print(f"Hola, soy {name}! y tengo {edad} años")
print(f"Hola, soy {name}! y tengo {edad} años. Mi nombre real es {name}")
```