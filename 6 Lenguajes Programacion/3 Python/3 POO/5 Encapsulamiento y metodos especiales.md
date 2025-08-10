# Encapsulamiento y métodos especiales 

Tags: #Python3 

El encapsulamiento en la programación orientada a objetos (POO) maneja principalmente tres niveles de visibilidad para los atributos y métodos de una clase: públicos, protegidos y privados. En Python, esta distinción se realiza mediante convenciones en la nomenclatura, más que a través de estrictas restricciones de acceso como en otros lenguajes.

**Atributos Públicos**
Son accesibles desde cualquier parte del programa y, por convención, no tienen un prefijo especial. Se espera que estos atributos sean parte de la interfaz permanente de la clase.

**Atributos Protegidos**
Se indica con un único guion bajo al principio del nombre (por ejemplo, ‘**_atributo**‘). Esto es solo una convención y no impide el acceso desde fuera de la clase, pero se entiende que estos atributos están protegidos y no deberían ser accesibles directamente, excepto dentro de la propia clase y en subclases.

**Atributos Privados**
En Python, los atributos privados se indican con un doble guion bajo al principio del nombre (por ejemplo, ‘**__atributo**‘). Esto activa un mecanismo de cambio de nombre conocido como ‘**name mangling**‘, donde el intérprete de Python altera internamente el nombre del atributo para hacer más difícil su acceso desde fuera de la clase.

**Métodos Especiales**
Los métodos especiales en Python son también conocidos como métodos mágicos y son identificados por doble guion bajo al inicio y al final (‘**__metodo__**‘). Permiten a las clases en Python emular el comportamiento de los tipos incorporados y responder a operadores específicos. Por ejemplo, el método ‘**__init__**‘ se utiliza para inicializar una nueva instancia de una clase, ‘**__str__**‘ se invoca para una representación en forma de cadena legible del objeto y ‘**__len__**‘ devuelve la longitud del objeto.

Algunos métodos especiales importantes en POO son:
- **__init__(self, […])**: Inicializa una nueva instancia de la clase.
- **__str__(self)**: Devuelve una representación en cadena de texto del objeto, invocado por la función ‘**str(object)**‘ y ‘**print**‘.
- **__repr__(self)**: Devuelve una representación del objeto que debería, en teoría, ser una expresión válida de Python, invocado por la función ‘**repr(object)**‘.
- **__eq__(self, other)**: Define el comportamiento del operador de igualdad ‘**==**‘.
- **__lt__(self, other), __le__(self, other), __gt__(self, other), __ge__(self, other)**: Definen el comportamiento de los operadores de comparación (**<**, **<=**, **>** y **>=** respectivamente).
- **__add__(self, other), __sub__(self, other), __mul__(self, other), etc.**: Definen el comportamiento de los operadores aritméticos (**+**, **–**, *****, etc.).

El encapsulamiento y los métodos especiales son herramientas poderosas que, cuando se utilizan correctamente, pueden mejorar la seguridad, la flexibilidad y la claridad en la construcción de aplicaciones. A lo largo de esta clase, exploraremos en detalle cómo implementar y utilizar estos conceptos y métodos para crear clases robustas y mantenibles en Python.

## Encapsulamiento

```python 
#!/usr/bin/env python3 

class Ejemplo:
	def __init__(self):
		self._atributo_protegido = "Soy un atributo protegido y no deberias verme"   # Creamos un atributo protegido
		self.__atributo_privado = "Soy un atributo privado y no deberias verme"      # Creamos un atributo privado 

ejemplo = Ejemplo()
print(ejemplo._atributo_protegido)           # Este si lo podemos ver pero no deberia ser la intension 
print(ejemplo._Ejemplo__atributo_privado)    # Python hace un 'name mangling' por lo que debemos de agregar antes la clase 
```

```python 
#!/usr/bin/env python3 

class Coche:
	def __init__(self, marca, modelo):
		self.marca = marca 
		self.modelo = modelo 
		self.__kilometraje = 0                # Creamos un atributo privado  

	def conducir(self, kilometros):
		if kilometros >= 0:
			self.__kilometraje += kilometros
		else:
			print("\n[+] Los kilometros deben ser mayores a 0\n")

	def mostrar_kilometros(self):
		return self.__kilometraje

coche = Coche("Toyota", "Corolla")
coche.conducir(150)
print(coche.mostrar_kilometros())
```

## Métodos especiales 

```python 
#!/usr/bin/env python3 

class Libro:
	def __init__(self, autor, titulo):
		self.autor = autor
		self.titulo = titulo

	def __str__(delf):             # 'STR' es un metodo especial donde accedes a los atributos y puedes mostrarlos
		return f"El libro {self.titulo} ha sido escrito por {self.autor}"

	def __eq__(self, otro)   # Metodo especial de igualdades, si son iguales se aplica lo que este dentro del metodo 
		return self.autor == otro.autor and self.titulo == otro.titulo


libro = Libro("Omar", "Como ser un Lammer?")
libro_dos = Libro("Omar", "Como ser un Lammer?")


print(libro)
print(f"Son iguales ambos libros? -> {libro == libro_dos}")
```

```python 
#!/usr/bin/env python3 

class Caja:
	def __init__(self, *items)   # Esta variable almacenara todos los atributos como si fuera una 'tupla'
		self.items = items

	def mostrar_items(self):
		for item in self.items:
			print(item)

	def __len__(self):           # Metodo especial para obtener la longitud
		return len(self.items)

caja = Caja("Manzana", "Platano", "Kiwi", "Pera")
caja.mostrar_items()
print(len(caja))
```

```python 
#!/usr/bin/env python3 

class Pizza:
	def __init__(self, size, *ingredientes):
		self.size = size 
		self.ingredientes - ingredientes 

	def descripcion(self):
		print(f"Esta pizza tiene {self.size} cm de longitud y los ingredientes son {','.join(self.ingredientes)}") # Sirve para representar cada elemento de una forma especifica, en este caso con ','

pizza = Pizza(12, "Chorizo", "Jamon", "Bacon", "Queso", "Cebolla")
pizza.descripcion()
```

```python 
#!/usr/bin/env python3 

class MiLista:

	def __init__(self):
		self.data = [1,2,3,4,5]

	def __getitem__(self, index):   # Este metodo especial nos ayuda a obtener el indice de la lista
		return sel.data[index]

lista = MiLista()
print(lista[2])
```

```python 
#!/usr/bin/env python3 

class Saludo:
	def __init__(self, saludo):
		self.saludo = saludo 

	def __call__(self, nombre):
		return f"{self.saludo} {nombre}!"

hola = Saludo("Hola")
print(hola("Luis"))
print(hola("Alberto"))
```