# Control de flujo (Condicionales y Bucles)

Tags: #Python3 

**Bucles**
Los bucles permiten ejecutar un bloque de código repetidamente mientras una condición sea verdadera o para cada elemento en una secuencia. Los dos tipos principales de bucles que utilizamos en Python son ‘**for**‘ y ‘**while**‘.

- **for**: Se usa para iterar sobre una secuencia (como una lista, un diccionario, una tupla o un conjunto) y ejecutar un bloque de código para cada elemento de la secuencia.
- **while**: Ejecuta un bloque de código repetidamente mientras una condición específica se mantiene verdadera.

**Control de Flujo en Bucles**
Existen declaraciones de control de flujo que pueden modificar el comportamiento de los bucles, como ‘**break**‘, ‘**continue**‘ y ‘**pass**‘.

- **break**: Termina el bucle y pasa el control a la siguiente declaración fuera del bucle.
- **continue**: Omite el resto del código dentro del bucle y continúa con la siguiente iteración.
- **pass**: No hace nada, se utiliza como una declaración de relleno donde el código eventualmente irá, pero no ha sido escrito todavía.


```python
#!/usr/bin/env python3 

for i in range(5)                             # Imprimimos 5 numeros, desde el 0 hasta el 4 
	print(i)


names = ["Omar", "Juan", "Pedro", "Santi"]    # Imprimimos todos los nombres de la lista
for name in names:
	print(f"El nombre es {name}")


for i, name in enumerate(names):              # Enumerate siempre te devuelve (Posicion, Valor), por eso debes colocar dos variables
	print(f"Nombre [{i+1}]: {name}")
```

```python
#!/usr/bin/env python3 

for i in range(10):          # Aqui decimos que cuando i = 5, que rompa el ciclo 
	if i == 5
	break 
	print(i)


for i in range(10):          # Aqui decimos que cuando i = 5, que no haga nada y continue a la siguiente iteracion
	if i == 5
	continue 
	print(i)


i = 0
while i < 5:                 # Imprimimos 5 numeros, desde el 0 hasta el 4 
	print(i)
	i += 1
```

```python 
#!/usr/bin/env python3 

frutas = {"manzana" : 1, "platanos" : 5, "Kiwis" : 3}    # Usamos un diccionario

for fruta, cantidad in frutas.items():
	print(f"Hay {cantidad} tantas cantidades para la fruta {fruta} ")
```

```python 
#!/usr/bin/env python3 

for i in range(10):         # Los ciclos 'for' tambien tienen un 'else' y este se ejecuta si sales del bucle exitosamente
	if i == 10:
		break 
else:
	print("Bucle concluido exitosamente")


i=0
while i < 10:               # Los ciclos 'while' tambien tienen un 'else' y este se ejecuta si sales del bucle exitosamente
	if i == 10:
		break 
	i += 1
else: 
	print("Bucle concluido exitosamente")
```

* Bubles anidados 

```python 
#!/usr/bin/env python3 

lista = [[1, 4, 5], [2, 6, 8], [9, 4, 1]]

for element in lista:
	for element_in_list in element:
		print(element_in_list)
```


**Condicionales**
Los condicionales son estructuras de control que permiten ejecutar diferentes bloques de código dependiendo de si una o más condiciones son verdaderas o falsas. En Python, las declaraciones condicionales más comunes son ‘**if**‘, ‘**elif**‘ y ‘**else**‘.
- **if**: Evalúa si una condición es verdadera y, de ser así, ejecuta un bloque de código.
- **elif**: Abreviatura de “**else if**“, se utiliza para verificar múltiples expresiones sólo si las anteriores no son verdaderas.
- **else**: Captura cualquier caso que no haya sido capturado por las declaraciones ‘**if**‘ y ‘**elif**‘ anteriores.

```python 
#!/usr/bin/env python3 

edad = 12

if < 13:
	print("Eres un niño")
elif 13 <= edad < 18:
	print("Eres un adolecente")
else:
	print("Eres un adulto")



# 'AND' significa que debemos de cumplir las dos condicionales 
# 'OR' significa que podemos cumplir una sola condicional 
# '!=' significa no es 

nacionalidad = "mexicana"

if edad >= 18 and nacionalidad == "mexicana":
	print("Puedes votar en Mexico")
else:
	print("No puedes votar")



lista = [1, 2, 3, 4, 5, 6, 7]

if 5 in lista:
	print("Este numero esta en la lista")
else:
	print("Este numero NO esta en la lista")
```

* Condicionales anidados 

```python
#!/usr/bin/env python3 

edad = 20 
nacionalidad = "mexicana"

if edad >= 18:
	if nacionalidad = "mexicana":
		print("Puede votar en Mexico")

```