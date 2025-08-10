# Comprendiendo el método Self

Tags: #Python3 

El uso de self es uno de los aspectos más fundamentales y a la vez confusos para quienes se adentran en la Programación Orientada a Objetos (POO) en Python. Este identificador es crucial para entender cómo Python maneja los métodos y atributos dentro de sus clases y objetos.

**Definición de ‘self’**
A nivel conceptual, ‘**self**‘ es una referencia al objeto actual dentro de la clase. Es el primer parámetro que se pasa a cualquier método de una clase en Python. A través de self, un método puede acceder y manipular los atributos del objeto y llamar a otros métodos dentro del mismo objeto.

**Uso de ‘self’**
Cuando se crea una nueva instancia de una clase, Python pasa automáticamente la instancia recién creada como el primer argumento al método ‘**__init__**‘ y a otros métodos definidos en la clase que tienen self como su primer parámetro. Esto es lo que permite que un método opere con datos específicos del objeto y no con datos de la clase en general o de otras instancias de la clase.

**Importancia de ‘self’**
El concepto de self es importante en la POO ya que asegura que los métodos y atributos se apliquen al objeto correcto. Sin self, no podríamos diferenciar entre las operaciones y datos de diferentes instancias de una clase.

En esta clase, nos enfocaremos en comprender a fondo cómo y por qué self es usado en Python, explorando su papel en la interacción con las instancias de la clase. Desarrollaremos una comprensión clara de cómo self permite que las clases en Python sean intuitivas y eficientes, manteniendo un estado consistente a través de las operaciones del objeto. Este conocimiento es esencial para trabajar con clases y objetos de manera efectiva y aprovechar la potencia de la POO.


```python
#!/usr/bin/env python3 

class Persona:
	def __init__(self, nombre, edad):           # Creamos el constructor, cons 'self' haces referencia al objeto 
		self.nombre = nombre
		self.edad = edad

	def presentacion(self):                     # Creamos un metodo 
		print(f"Hola soy {self.nombre} y tengo {self.edad}.")

omar = Persona("Omar", 29)                       # Objeto
omar.presentacion()                             
```

```python
#!/usr/bin/env python3 

class Calculadora:
	def __init__(self, numero):
		self.numero = numero 

	def suma(self, otro_numero):
		return self.numero + otro_numero           # Te devuelve la suma de dos numeros

	def doble_suma(self, num1, num2):
		print(self.suma(num1) + self.suma(num2))   # Referencias otro metodo para poder usarlo en este metodo 

calc = Calculadora(5)
calc.suma(8)
calc.doble_suma(2, 9)
```