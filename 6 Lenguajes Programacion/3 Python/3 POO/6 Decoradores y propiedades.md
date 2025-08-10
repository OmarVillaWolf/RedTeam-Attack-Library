# Decoradores y Propiedades 

Tags: #Python3 

**Decoradores**
Los decoradores en Python son una forma elegante de modificar las funciones o métodos. Se utilizan para extender o alterar el comportamiento de la función sin cambiar su código fuente. Un decorador es en sí mismo una función que toma otra función como argumento y devuelve una nueva función que, opcionalmente, puede agregar alguna funcionalidad antes y después de la función original.

**Propiedades**
Las propiedades son un caso especial de decoradores que permiten a los desarrolladores añadir “**getter**“, “**setter**” y “**deleter**” a los atributos de una clase de manera elegante, controlando así el acceso y la modificación de los datos. En Python, esto se logra con el decorador ‘**@property**‘, que transforma un método para acceder a un atributo como si fuera un atributo público.

**Getters y Setters**
- El “**getter**” obtiene el valor de un atributo manteniendo el encapsulamiento y permitiendo que se ejecute una lógica adicional durante el acceso.
- El “**setter**” establece el valor de un atributo y puede incluir validación o procesamiento antes de que el cambio se refleje en el estado interno del objeto.
- El “**deleter**” puede ser utilizado para definir un comportamiento cuando un atributo es eliminado con del.

## Decorador

```python 
#!/usr/bin/env python3 

def mi_decorador(funcion):             # Creamos un decorador llamado 'Funcion de orden superior'
	def envoltura():                  # Creamos una 'envoltura' que es otra funcion 
		print("Estoy saludando en la envoltura del decorador antes de llamar a la funcion")
		funcion()
		print("Estoy saludando en la envoltura del decorador despues de llamar a la funcion")
	return envoltura 

@mi_decorador                           # Forma de colocar llamar al decorador 
def saludo():
	print("Hola, estoy saludando dentro de la funcion")

saludo()
```

```python 
#!/usr/bin/env python3 

import time 

def cronometro(funcion):
	def envoltura(num):
		inicio = time.time()
		funcion(num)
		final = time.time()
		print(f"Tiempo total transcurrido en la funcion {funcion.__name__}: {final - inicio}")
	return envoltura 

@cronometro
def pausa_corta(num):
	time.sleep(num)

@cronometro
def pausa_larga(num):
	time.sleep(num)

pausa_corta(2)   # Cuando pasamos un valor, debemos de agregar la variable en todas las funciones 
pausa_larga(3)
```

## Propiedad 

```python 
#!/usr/bin/env python3 

class Persona:
	def __init__(self, nombre, edad):
		self._nombre = nombre   # Variable protegida
		self._edad = edad 

	@property                    # Creamos una propiedad 
	def edad(self):              # Creamos el Getter
		return self._edad       # Regresa el atriubuto privado 'protegido'

	@edad.setter                 # Configuramos el Setter
	def edad(self, valor):
		if valor > 0:
			self._edad = valor
		else:
			raise ValueError("[!] La edad no puede ser 0 o un numero negativo")

manolo = Persona("Manolo", 23)
manolo.edad = 14           # Setter
print(manolo.edad)         # Getter
```

## Argumento 'args'

```python 
# Caso cuando usamos 'args' en donde recibira una serie de elementos tipo 'tupla' logrando iterar sobre ellos 

#!/usr/bin/env python3 

def suma(*args):
	return sum(args)

print(suma(2, 3, 4, 6, 12, 14, 15, 87))
```

## Argumento 'kwargs'

```python 
# Caso cuando usamos 'kwargs' en donde recibira una serie de elementos tipo 'diccionario' logrando iterar sobre ellos 

#!/usr/bin/env python3 

def presentacion(**kwargs):
	for clave, valor in kwargs.items():
		print(f"{clave}: {valor}")

presentacion(nombre="Omar", edad=28, pais="Mexico", profesion="Lammer")
```