# Tuplas 

Tags: #Python3 

Las tuplas son colecciones ordenadas de elementos que no pueden modificarse una vez creadas. Esta característica las hace ideales para asegurar que ciertos datos permanezcan constantes a lo largo del ciclo de vida de un programa.

**Características de las Tuplas**
- **Inmutabilidad**: Una vez que se crea una tupla, no puedes cambiar, añadir o eliminar elementos. Esta inmutabilidad garantiza la integridad de los datos que desea mantener constantes.
- **Indexación y Slicing**: Al igual que las listas, puedes acceder a los elementos de la tupla mediante índices y también puedes realizar operaciones de slicing para obtener subsecuencias de la tupla.
- **Heterogeneidad**: Las tuplas pueden contener elementos de diferentes tipos, incluyendo otras tuplas, lo que las hace muy versátiles.

**Operaciones con Tuplas**
Aunque no puedes modificar una tupla, hay varias operaciones que puedes realizar:

- **Empaquetado y Desempaquetado de Tuplas**: Las tuplas permiten asignar y desasignar sus elementos a múltiples variables de forma simultánea.
- **Concatenación y Repetición**: Similar a las listas, puedes combinar tuplas usando el operador ‘**+**‘ y repetir los elementos de una tupla un número determinado de veces con el operador ‘*****‘.
- **Métodos de Búsqueda**: Puedes usar métodos como ‘**index()**‘ para encontrar la posición de un elemento y ‘**count()**‘ para contar cuántas veces aparece un elemento en la tupla.

**Uso de Tuplas en Python**

- **Funciones y Asignaciones Múltiples**: Las tuplas son muy útiles cuando una función necesita devolver múltiples valores o cuando se realizan asignaciones múltiples en una sola línea.
- **Estructuras de Datos Fijas**: Se usan para crear estructuras de datos que no deben cambiar, como los días de la semana o las coordenadas de un punto en el espacio.


```python 
#!/usr/bin/env python3

tupla1 = (1, 2, 3, 4, 5)                # Definir una tupla 

print(tupla[0])                        # Asi imprimimos el primer elemento de la tupla
print(tupla[1:3])
print(tupla[-1])


tupla2 = (1, 'test', [1, 2, 3], True, 5) 

for i in tupla:
	print(i)


tupla3 = (1, 2, 3, 4)
a, b, c, d = tupla3
print(a)
print(b)
print(c)
print(d)
print(len(tupla))


tupla4 = tupla1 + tupla 3
tupla4 = tupla1 * 3
print(tupla4)
```