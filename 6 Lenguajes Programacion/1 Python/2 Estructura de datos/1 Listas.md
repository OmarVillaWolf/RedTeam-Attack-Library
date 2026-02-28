# Listas 

Tags: #Python3 

Las listas son estructuras de datos que nos permiten almacenar secuencias ordenadas de elementos. Son mutables, lo que significa que podemos modificarlas después de su creación, y son dinámicas, permitiéndonos añadir o quitar elementos de ellas.

**Características de las Listas**
Vamos a explorar las características clave de las listas en Python, que incluyen su capacidad para:

- Almacenar datos heterogéneos, es decir, pueden contener elementos de diferentes tipos (enteros, cadenas, flotantes y más) dentro de una misma lista.
- Ser indexadas y cortadas, lo que permite acceder a elementos específicos de la lista directamente a través de su índice.
- Ser anidadas, es decir, una lista puede contener otras listas como elementos, lo que permite crear estructuras de datos complejas como matrices.

**Operaciones con Listas**
También cubriremos las operaciones fundamentales que se pueden realizar con listas, como:

- Añadir elementos con métodos como ‘**append()**‘ y ‘**extend()**‘.
- Eliminar elementos con métodos como ‘**remove()**‘ y ‘**pop()**‘.
- Ordenar las listas con el método ‘**sort()**‘ o la función incorporada ‘**sorted()**‘.
- Invertir los elementos con el método ‘**reverse()**‘ o la sintaxis de corte ‘**[::-1]**‘.
- Comprender las comprensiones de listas, una forma “pythonica” de crear y manipular listas de manera concisa y eficiente.

**Métodos de Listas**
Profundizaremos en la rica gama de métodos que Python ofrece para trabajar con listas y cómo estos métodos pueden ser utilizados para manipular listas de acuerdo a nuestras necesidades

```python
# En las listas, la posición del primer elemento siempre inicia en '0'  --> [0 1 2 3 4 5 ...]

#!/usr/bin/env python3

my_ports = [80, 443, 445, 8080]             # Definimos una lista, esta tambien puede ser una lista vacia
my_ports.append(22)                         # Con 'append' podemos agregar un valor a la lista, este valor es agregado al final
my_ports.extend([23, 45])                   # Con 'extend' podemos agregar varios valores a la vez a la lista
my_ports += [99, 100]                       # Otra forma de agregar elementos a la lista 
my_ports.insert(2, 9)                       # Para hacer una sustitucion de un valor en una posicion especifica (Posicion, Valor), Pd: No borras el valor anterior
my_ports.insert(2, "Hola")

my_ports = sorted(my_ports)                 # Nos ayuda a ordenar la lista 
set(my_ports)                               # Te quita los valores repetidos de la lista pero te la convierte en un 'set = {}'

del my_ports[0]                             # Para que nos elimine el elemento de la lista que en su posición es 0 (primer elemento)
my_ports.pop()                              # Para eliminar el ultimo elemento de la lista 
my_ports.remove(443)                        # Quitas un elemento especifico de la lista

my_ports.clear()                            # Limpias la lista 'Eliminas todos los elementos'

my_ports.reverse()                          # Invierte los elementos de la lista

my_ports.index(445)                         # Para conocer el indice de ese elemento (Posicion en la lista), 'si hay elementos repetidos, solo te muestra el primero'

my_ports.count(80)                          # Para contar el numero de veces que sale ese valor en la lista 

for port in my_ports:                       # Imprimiremos cada elemento de la lista
	print("Puerto: " + str(port))           
	print(f"Puerto: {port}")               # f = String Formating

print(f"\n[+] La lista tiene un total de {len(my_ports)} elementos")     
	# len = Nos muestra la longitud, en este caso la longitud de la lista 
	# \n = Salto de linea

```

```python
# Listas 

mi_lista = [1, 2, 3, 4, 5]

print(mi_lista[0])           # Solo me muestra el elemento que tiene esa posicion 
print(mi_lista[:2])          # Aqui me va a mostrar los elementos empezando de izquierda hasta la posicion 2 (-1)
print(mi_lista[0:3])         # Aqui me va a mostrar los elementos desde la posicion 0 hasta la 3 (-1)
print(mi_lista[2:4])         # Aqui me va a mostrar los elementos desde la posicion 2 hasta la 4 (-1)

print(mi_lista[2:])          # Aqui me va a mostrar los elementos empezando desde la posicion 2 hasta donde termina la lista

print(mi_lista[-1])          # Me va a mostrar el ultimo elemento de la lista ya que '-1' significa: Empezar desde el final 
print(mi_lista[-2:])         # Aqui empezara desde la posicion de -2 y terminara con todos los elementos de la derecha 
print(mi_lista[:-2])         # Aqui muestra todos los elementos de la izquierda y termina en la posicion -2, pero ese elemento (-2) no se cuenta
```

```python
# Listas 

mi_lista = [1, 2, 3, 4, 5]

enumerate(mi_lista)                    # Esto me devuelve dos elementos (indices y los valores de la lista) 

for x, y in enumerate(mi_lista):       # Me va a mostrar por consola los indice y valores de la lista (x = indices, y = valores)
	print(f"{x}: {y}")
```

```python 
# Listas 

mi_lista = [1, 2, 3, 4, 5]

max(mi_lista)                                             # Muestra el valor maximo de la lista
min(mi_lista)                                             # Muestra el valor minimo de la lista 
sum(mi_lista)                                             # Sumas todos los valores de la lista
len(mi_lista)                                             # Muestra el total de elementos en la lista 
sum(mi_lista) / len(mi_lista)                             # Calcular el promedio 
round(sum(mi_lista) / len(mi_lista), 2)                   # Redondear los decimales en este caso a 'dos' elementos 

print(f"[+] El numero mas alto es {max(mi_lista)}")       # Para obtener el valor mas alto de la lista  
print(f"[+] El numero mas bajo es {min(mi_lista)}")       # Para obtener el valor mas bajo de la lista
```

```python 
# Listas 

one_list = [1, 3, 5, 7, 9]
second_list = [2, 4, 6, 8, 10]

result = list(map(sum, zip(one_list, second_list)))             # Zip te junta los valores en tuplas, ejemplo: (1, 2), (3, 4)
	# Map = Hace que puedas meter una funcion, en este caso 'sum' y un iterable para funcionar 
	# Con el 'Tipe Casting', convertimos el resultado en lista 

print(result)
```

```python 
# Listas 

lista = ['Phishing', 'DDoS', 'SQL Injection', 'Man in the middle', 'Cross-side Scripting']

attacks = [attack.upper() for attack in lista]             # Por cada elemento de la lista, le haremos un Upper (Lo convertiremos a mayusculas)
attacks = [attack.lower() for attack in lista]             # Por cada elemento de la lista, le haremos un Lower (Lo convertiremos a minusculas)
print(attacks)


edades = [12, 45, 23, 56, 65]

for ataques, edad in zip(lista, edades)
	print(f"{ataques} tiene {edad} años")
```