# Variables y tipos de datos 

Tags: #Python3 

Las variables en Python son como nombres que se le asignan a los datos que manejamos. Piensa en una variable como un nombre que pones a un valor, para poder referirte a él y utilizarlo en diferentes partes de tu código.

En la clase actual, vamos a enfocarnos en comprender las variables y algunos de los tipos de datos fundamentales en Python. Estos conceptos son esenciales, ya que nos permiten almacenar y manipular la información en nuestros programas.

**Variables**
Una variable en Python es como un nombre que se le asigna a un dato. No es necesario declarar el tipo de dato, ya que Python es inteligente para inferirlo.

**Cadenas (Strings)**
Las cadenas son secuencias de caracteres que se utilizan para manejar texto. Son inmutables, lo que significa que una vez creadas, no puedes cambiar sus **caracteres individuales**.

**Números**
Python maneja varios tipos numéricos, pero nos centraremos principalmente en:
- **Enteros (Integers)**: Números sin parte decimal.
- **Flotantes (Floats)**: Números que incluyen decimales.

```python 
# Si tenemos una variable 'int' con el método 'Type Casting' podemos convertirla a 'float'

#!/usr/bin/env python3

number = float(5)      # Declaramos un numero entero pero colocandole el float, este sera un numero flotante 
numb = str(4)          # Convertimos el numero 'int' a cadena 'string'
print(number)          # Imprimimos el numero por consola
print(type(number))    # Nos muestra el tipo de dato, en este caso será flotante y no entero 

```

**Listas**
Las listas son colecciones ordenadas y mutables que pueden contener elementos de diferentes tipos. Son ideales para almacenar y acceder a secuencias de datos.
Y para trabajar con estas listas, así como con cadenas y rangos de números, utilizaremos los bucles ‘**for**‘, que nos permiten iterar sobre cada elemento de una secuencia de manera eficiente.

