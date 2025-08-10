# Métodos estáticos y métodos de clase

Tags: #Python3 

**Métodos de Clase**
Se definen con el decorador ‘**@classmethod**‘, lo que les permite tomar la clase como primer argumento, generalmente nombrada ‘**cls**‘. Este acceso a la clase permite que los métodos de clase interactúen con la estructura de la clase en sí, como modificar atributos de clase que afectarán a todas las instancias. Se utilizan para tareas que requieren conocimiento del estado global de la clase, como la construcción de instancias de maneras específicas, también conocidos como métodos factory.

**Métodos Estáticos**
Se definen con el decorador ‘**@staticmethod**‘ y no reciben un argumento implícito de referencia a la clase o instancia. Son similares a las funciones regulares definidas dentro del cuerpo de una clase. Se utilizan para funciones que, aunque conceptualmente pertenecen a la clase debido a la relevancia temática, no necesitan acceder a ningún dato específico de la clase o instancia. Proporcionan una manera de encapsular la funcionalidad dentro de una clase, manteniendo la cohesión y la organización del código.

```python
#!/usr/bin/env python3 

class Calculadora:

	@staticmethod
	def suma(num1, num2)
		return num + num2 

	@staticmethod
	def resta(num1, num2)
		return num - num2 

	@staticmethod
	def multiplicacion(num1, num2)
		return num * num2 

print(Calculadora.suma(2, 8))
print(Calculadora.resta(8, 4))
print(Calculadora.multiplicacion(5, 10))
```

