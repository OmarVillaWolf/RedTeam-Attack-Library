# Formateo de cadenas y manipulación de texto 

Tags: #Python3 

El formateo de cadenas y la manipulación de texto son habilidades esenciales en Python, especialmente en aplicaciones que involucran la presentación de datos al usuario o el procesamiento de información textual. En esta clase, nos centraremos en las técnicas y herramientas que Python ofrece para trabajar con cadenas de texto.

**Formateo de Cadenas**
Aprenderemos los distintos métodos de formateo de cadenas que Python proporciona, incluyendo:

- **Formateo Clásico**: A través del operador %, similar al ‘**printf**‘ en C.
- **Método format()**: Un enfoque versátil que ofrece numerosas posibilidades para formatear y alinear texto, rellenar caracteres, trabajar con números y más.
- **F-strings (Literal String Interpolation)**: Introducido en Python 3.6, este método permite incrustar expresiones dentro de cadenas de texto de una manera concisa y legible.

**Manipulación de Texto**
Exploraremos las funciones y métodos incorporados para la manipulación de cadenas, que incluyen:

- **Métodos de Búsqueda y Reemplazo**: Como ‘**find()**‘, ‘**index()**‘, ‘**replace()**‘ y métodos de expresiones regulares.
- **Métodos de Prueba**: Para verificar el contenido de la cadena, como ‘**isdigit()**‘, ‘**isalpha()**‘, ‘**startswith()**‘, y ‘**endswith()**‘.
- **Métodos de Transformación**: Que permiten cambiar el caso de una cadena, dividirla en una lista de subcadenas o unirlas, como ‘**upper()**‘, ‘**lower()**‘, ‘**split()**‘, y ‘**join()**‘.


```python 
#!/usr/bin/env python3 

c = "    hola, Mundo!!"

print(c.strip())       # Te suprime todos los espacios, saltos de linea, tabulaciones, etc...
print(c.lower())       # Coloca la cadena en minusculas 
print(c.uper())        # Coloca la cadena en mayusculas
print(c.replace('o', 'x'))       # Reemplaza la letra 'o' por la 'x' en toda la cadena
	print(c.replace(',', ''))   # Reemplaza la coma por 'nada'
print(c.split())       # Crea una lista con elementos y los delimita por una coma ',' refiriendose al espacio
	print(c.split(:)) # Puedes hacer mencion a que elemento de la cadena quieres usar como delimitador 
print(c.startswith('ho'))   # Nos devuelve un 'True' si la cadena empieza con 'Ho' 
print(c.endswith('!!'))     # Nos devuelve un 'False' si la cadena termina con '!!' 
print(c.find('Mundo'))      # Busca ocurrencias en una cadena de texto y devuelve la posicion inicial de la palabra
print(c.index('Mundo'))     # Busca ocurrencias en una cadena de texto y si no existe te marca un error 
print(c.count('o'))         # Cuenta todas las veces que sale el caracter 'o' en toda la cadena
print(c.capitalize())       # Convierte el primer caracter de la cadena a mayuscula
print(c.title())            # Convierte todos los primeros caracteres de la cadena a mayusculas 
print(c.swapcase())         # Convierte las mayusculas a minusculas y viceversa
print(c.isalpha())          # Verifica si la cadena esta compuesta por caracteres del alfabeto y devuelve un 'True'
print(c.isdigit())          # Verifica si la cadena esta compuesta por caracteres numericos y devuelve un 'True'
print(c.isspace())          # Verifica si la cadena esta compuesta por un espacio y devuelve un 'True'
print(c.islower())          # Verifica si la cadena esta compuesta por minusculas y devuelve un 'True'
print(c.isupper())          # Verifica si la cadena esta compuesta por mayusculas y devuelve un 'True'
print(c.istitle())          # Verifica si la cadena esta compuesta por palabras de titulo y devuelve un 'True'
```

```python 
#!/usr/bin/env python3 

c = ["Hola", "Mundo"]

print(' '.join(c))          # Representar a los elementos separados por un espacio 
```

```python 
#!/usr/bin/env python3 

s = "Hola soy Omar y no me gusta la playa"

tabla = str.maketrans('aeo', 'zpi')     # Decimos que reemplazce la 'a' por 'z', la 'e' por 'p' y asi sucesivamente
nueva_cadena = s.translate(tabla)
print(nueva_cadena)
```