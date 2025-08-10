# Clases y Objetos 

Tags: #Python3 

**Clases**
Las clases son los fundamentos de la POO. Actúan como plantillas para la creación de objetos y definen atributos y comportamientos que los objetos creados a partir de ellas tendrán. En Python, una clase se define con la palabra clave ‘**class**‘ y proporciona la estructura inicial para todo objeto que se derive de ella.

**Instancias de Clase y Objetos**
Un objeto es una instancia de una clase. Cada vez que se crea un objeto, se está creando una instancia que tiene su propio espacio de memoria y conjunto de valores para los atributos definidos por su clase. Los objetos encapsulan datos y funciones juntos en una entidad discreta.

**Métodos de Clase**
Los métodos de clase son funciones que se definen dentro de una clase y solo pueden ser llamados por las instancias de esa clase. Estos métodos son el mecanismo principal para interactuar con los objetos, permitiéndoles realizar operaciones o acciones, modificar su estado o incluso interactuar con otros objetos.

```python 
#!/usr/bin/env python3 

class Persona:                                # Así creamos una clase 
	def __init__(self, nombre, edad):        # Así creamos un contructor (método)
		self.nombre = nombre
		self.edad = edad 

	def saludo(self):
		return f"Hola soy {self.nombre} y tengo {self.edad} años"

omar = Persona("Omar", 28)                    # Así creamos un objeto con sus atributos (Instancia)
print(omar.saludo())
```

```python 
#!/usr/bin/env python3 

class Animal:
	def __init__(self, nombre, animal):
		self.nombre = nombre
		self.animal = animal 

	def descripcion(self):
		print(f"{self.nombre} es un {self.animal}")

gato = Animal("Pipo", "Gato")
perro = Animal("Tobby", "Perro")

gato.descripcion()
perro.descripcion()
```

```python
#!/usr/bin/env python3

class CuentaBanc:
	def __init__(self, cuenta, nombre, dinero=0):    # Si no le pasas un valor a la var dinero, esta por defecto va a ser 0
		self.cuenta = cuenta 
		self.nombre = nombre
		self.dinero = dinero

	def Depositar(self, deposito):
		self.dinero += deposito
		return f"\n[+] [{self.nombre}] Se han depositado {deposito} dolares, actualmente tienes {self.dinero} dolares"

	def Retirar(self, retirar)
		if retirar > self.dinero:
			return f"\n[!] [{self.nombre}] Operación denegada: Fondos insuficientes\n"
		self.dinero -= retirar 
		return f"\n[+] [{self.nombre}] Se han retirado {retirar} dolares, actualmente tienes {self.dinero} dolares"

omar = CuentaBanc("123345", "Omar Villa", 1000)

print(omar.Depositar(500))
print(omar.Retirar(200))
```

**Decoradores**
Los decoradores son una herramienta poderosa en Python que permite modificar el comportamiento de una función o método. Funcionan como “envoltorios”, que agregan funcionalidad antes y después del método o función decorada, sin cambiar su código fuente. En POO, los decoradores son frecuentemente utilizados para agregar funcionalidades de manera dinámica a los métodos, como la sincronización de hilos, la memorización de resultados o la verificación de permisos.

**Métodos de Clase**
Un método de clase es un método que está ligado a la clase y no a una instancia de la clase. Esto significa que el método puede ser llamado sobre la clase misma, en lugar de sobre un objeto de la clase. Se definen utilizando el decorador ‘**@classmethod**‘ y su primer argumento es siempre una referencia a la clase, convencionalmente llamada ‘**cls**‘. Los métodos de clase son utilizados a menudo para definir métodos “factory” que pueden crear instancias de la clase de diferentes maneras.

**Métodos Estáticos**
Los métodos estáticos, definidos con el decorador ‘**@staticmethod**‘, no reciben una referencia implícita ni a la instancia (self) ni a la clase (cls). Son básicamente como funciones regulares, pero pertenecen al espacio de nombres de la clase. Son útiles cuando queremos realizar alguna funcionalidad que está relacionada con la clase, pero que no requiere acceder a la instancia o a los atributos de la clase.

```python 
#!/usr/bin/env python3 

class Rectangulo:
	def __init__(self, ancho, alto):
		self.ancho = ancho 
		self.alto = alto

	@property       # Declaramos un decorador
	def area(self):
		return self.ancho * self.alto

	def __str__(self):                            # Método especial 'str'
		return f"\n[+] Propiedades del rectangulo: [Ancho: {self.ancho}][Alto: {self.alto}]"

	def __eq__(self, otro):                       # Método especial de igualdad
		return self.ancho == otro.ancho and self.alto == otro.alto

rect1 = Rectangulo(20, 80)
rect2 = Rectangulo(10, 60)

print(rect1)    # Debes de poner el método 'str' para que en lugar de que te salga que es un objeto te muestre sus propiedades
print(f"\n[+] El área es {rect1.area}") # Usas el decorador 'property' y así no es necesario usar '()' para llamar al método
print(f"\n[+] Son iguales? -> {rect1 == rect2}") # Usa el método 'eq' para poder comparar los dos objetos
```

```python 
#!/usr/bin/env python3 

class Libro:

	IVA = 0.21
	bestseller_value = 5000

	def __init__(self, titulo, autor, precio):
		self.titulo = titulo
		self.autor = autor 
		self.precio = precio

	@staticmethod    # Declaramos un decorador y este opera con los argumentos que le pasas en la función o variables de clase
	def bestseller(total_ventas): # Con el decorador 'staticmethod' podemos quitar el 'self'
		return total_ventas >  Libro.bestseller_value  # Así podemos usar una variable de la clase  

	@classmethod    # Este decorador recibe como argumento la propia clase 
	def precio_iva(cls, precio):
		return precio + precio * cls.IVA        # Así podemos usar una variable de la clase con el decorador 

mi_libro = Libro("Como ser buena onda?", "Omar", 12.5)
print(Libro.bestseller(5000))      # Con el decorador 'staticmethod' ahora debemos de colocar la clase
print(f"\n[+] El precio del libro con IVA incluido es de {Libro.precio_iva(mi_libro.precio)}") # Usando el decorador 'classmethod'
```