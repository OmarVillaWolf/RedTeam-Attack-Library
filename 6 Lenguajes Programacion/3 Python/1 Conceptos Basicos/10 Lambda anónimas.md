# Funciones lambda anónimas 

Tags: #Python3 

**Funciones Lambda**
Las funciones lambda son también conocidas como funciones anónimas debido a que no se les asigna un nombre explícito al definirlas. Se utilizan para crear pequeñas funciones en el lugar donde se necesitan, generalmente para una operación específica y breve. En Python, una función lambda se define con la palabra clave ‘**lambda**‘, seguida de una lista de argumentos, dos puntos y la expresión que desea evaluar y devolver.

Una de las ventajas de las funciones lambda es su simplicidad sintáctica, lo que las hace ideal para su uso en operaciones que requieren una función por un breve momento y para casos donde la definición de una función tradicional completa sería excesivamente verbosa.

**Usos comunes de las Funciones Lambda**
- **Con funciones de orden superior**: Como aquellas que requieren otra función como argumento, por ejemplo, ‘**map()**‘, ‘**filter()**‘ y ‘**sorted()**‘.
- **Operaciones simples**: Para realizar cálculos o acciones rápidas donde una función completa sería innecesariamente larga.
- **Funcionalidad en línea**: Cuando se necesita una funcionalidad simple sin la necesidad de reutilizarla en otro lugar del código.

```python 
#!/usr/bin/env python3 

mi_funcion = lambda: "Hola mundo"
print(mi_funcion())


cuadrado = lambda x: x**2                    # Para pasarle un argumento a la funcion lambda, colocamos una variable 
print(cuadrado(6))


suma = lambda x, y: x + y                    # Asi decimos que le pasaremos dos argumentos a la funcion lambda
print(suma(3, 5))
```

```python 
#!/usr/bin/env python3

num = [1, 2, 3, 4, 5]

cuadrado = list(map(lambda x: x**2, num))    # Map debe de llevar una funcion y un objeto iterable para funcionar 
print(cuadrado)
```

```python
#!/usr/bin/env python3

from functools import reduce                    # 'Reduce' te hace la operatoria para todos los iterables de la lista 

num = [1, 2, 3, 4, 5]

producto = reduce(lambda x, y: x*y, num)        # Debemos de pasarle a la funcion 'reduce' una funcion y un iterable 
print(producto)
```