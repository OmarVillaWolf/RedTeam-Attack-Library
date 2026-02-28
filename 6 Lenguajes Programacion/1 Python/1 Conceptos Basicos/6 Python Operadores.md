# Operadores básicos en Python

Tags: #Python3 

Los operadores aritméticos son símbolos que Python utiliza para realizar cálculos matemáticos.

Los fundamentales son:

- **Suma (+)**: No solo suma números, sino que también une secuencias como cadenas y listas, creando una nueva secuencia que es la combinación de ambas.
- **Resta (-)**: Se utiliza para restar un número de otro. Con listas, su uso es menos directo y generalmente no se aplica como operador directo.
- **Multiplicación (*)**: Cuando se multiplica un número por otro, obtenemos el producto. Con cadenas y listas, este operador repite los elementos la cantidad de veces especificada.
- **División (/)**: Divide un número entre otro y el resultado es siempre un número flotante, incluso si los números son enteros.
- **Exponente (**):** Eleva un número a la potencia de otro. Por ejemplo, ‘**2 ** 3**‘ resultará en 8. Este operador es menos común en operaciones con cadenas o listas.

**Operaciones con Cadenas**
En Python, las cadenas son objetos que representan secuencias de caracteres y se pueden manipular usando operadores aritméticos:

- **Concatenación (+)**: Une varias cadenas en una sola. Por ejemplo, ‘Hola’ + ‘ ‘ + ‘Mundo’ se convierte en ‘Hola Mundo’.
- **Repetición (*)**: Crea repeticiones de la misma cadena. ‘Hola’ * 3 generará ‘HolaHolaHola’.

**Operaciones con Listas**
Las listas son colecciones ordenadas y mutables de elementos:
- **Concatenación (+)**: Similar a las cadenas, unir dos listas las combina en una nueva lista.
- **Repetición (*)**: Repite todos los elementos de la lista un número determinado de veces.

**Funciones Especiales para Listas**
- **Zip**: Toma dos o más listas y las empareja, creando una lista de tuplas. Cada tupla contiene elementos de las listas originales que ocupan la misma posición.
- **Map**: Aplica una función específica a cada elemento de un iterable, lo que resulta útil para transformar los datos contenidos.

Asimismo, otro de los conceptos que mencionamos es el de ‘**TypeCast**‘. El TypeCast, o conversión de tipo, es el proceso mediante el cual se cambia una variable de un tipo de dato a otro.

En Python, esto se realiza de manera muy directa, utilizando el nombre del tipo de dato como una función para realizar la conversión. Por ejemplo, convertir una cadena a un entero se hace pasando la cadena como argumento a la función **int()**, y transformar un número a una cadena se hace con la función **str()**. Esta capacidad de cambiar el tipo de dato es especialmente útil cuando se necesita estandarizar los tipos de datos para operaciones específicas o para cumplir con los requisitos de las estructuras de datos.


```python
#!/usr/bin/env python3

a = 8
b = 6

# Operadores  
result = a + b          # Suma 
result = a - b          # Resta 
result = a * b          # Multiplicacion 
result = a / b          # Division 
result = a % b          # Modulo 
result = a ** b         # Potencia (8 elevado a la 6)
```

```python 
#!/usr/bin/env python3

a = 'Hola'
b = ' '
c = 'Mundo'

print(a + b + c)        # Podemos sumar los strings 
print(a*3)              # Podemos multiplicar el string por un numero y nos mostrara la misma palabra en este caso 3 veces 
print(a[0]*3)           # Le decimos que el primer caracter lo multiplique por 3 
print(a[0:3]*3)         # Le que desde el primer caracter hasta el tercero (-1), lo multiplique por tres 
```