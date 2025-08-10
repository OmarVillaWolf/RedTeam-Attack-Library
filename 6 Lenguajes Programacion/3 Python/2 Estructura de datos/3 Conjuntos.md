# Conjuntos 

Tags: #Python3 

**Características de los Conjuntos**
- **Unicidad**: Los conjuntos automáticamente descartan elementos duplicados, lo que los hace perfectos para recolectar elementos únicos.
- **Desordenados**: A diferencia de las listas y las tuplas, los conjuntos no mantienen los elementos en ningún orden específico.
- **Mutabilidad**: Los elementos de un conjunto pueden ser agregados o eliminados, pero los elementos mismos deben ser inmutables (por ejemplo, no puedes tener un conjunto de listas, ya que las listas se pueden modificar).

**Operaciones con Conjuntos**
Exploraremos las operaciones básicas de conjuntos que Python facilita, como:

- **Adición y Eliminación**: Añadir elementos con ‘**add()**‘ y eliminar elementos con ‘**remove()**‘ o ‘**discard()**‘.
- **Operaciones de Conjuntos**: Realizar uniones, intersecciones, diferencias y diferencias simétricas utilizando métodos o operadores respectivos.
- **Pruebas de Pertenencia**: Comprobar rápidamente si un elemento es miembro de un conjunto.
- **Inmutabilidad Opcional**: Usar el tipo ‘**frozenset**‘ para crear conjuntos que no se pueden modificar después de su creación.

**Uso de Conjuntos en Python**
- **Eliminación de Duplicados**: Son útiles cuando necesitas asegurarte de que una colección no tenga elementos repetidos.
- **Relaciones entre Colecciones**: Facilitan la comprensión y el manejo de relaciones matemáticas entre colecciones, como subconjuntos y superconjuntos.
- **Rendimiento de Búsqueda**: Proporcionan una búsqueda de elementos más rápida que las listas o las tuplas, lo que es útil para grandes volúmenes de datos.

```python 
#!/usr/bin/env python3 

conjunto = {1, 2, 3, 4, 5}       # Así creamos un conjunto y no es una estructura organizada
conjunto.add(4)                  # Agregas un elemento al conjunto 
conjunto.update({4, 5, 6})       # Agregas mas elementos al conjunto 
conjunto.remove(3)               # Eliminas un elemento del conjunto 
conjunto.discard(7)              # Elimina un elemento si existe en el conjunto y si no existe no hace nada (no muestra error)

print(conjunto)


conjunto2 = {3, 4, 5, 6, 7}
conjunto_final = conjunto.intersection(conjunto2) # Haces una intercepción y los elementos que se repiten son los que se muestran
conjunto_final = conjunto.difference(conjunto2)   # Los elementos que no se repiten son los que muestran
print(conjunto_final)


conjunto_final = conjunto.union(conjunto2)        # Haces una unión entre conjuntos y los elementos que se repiten los quita
print(conjunto_final)


lista = [1,2,1,1,2,3,4,5]          # Convertiremos la lista a un conjunto, el cual hará que no existan elementos repetidos
no_repeat = set(lista)
print(no_repeat)  


conjunto2 = set(range(10000))      # Creamos un conjunto desde el 0 hasta el 99999 
print(1234 in conjunto2)           # Buscamos un elemento especifico en el conjunto y nos devuelve un 'True/False'
```