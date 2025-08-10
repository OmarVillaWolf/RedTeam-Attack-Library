# Funciones 

Tags: #Python3 

**Funciones**
Las funciones son bloques de código reutilizables diseñados para realizar una tarea específica. En Python, se definen usando la palabra clave ‘**def**‘ seguida de un nombre descriptivo, paréntesis que pueden contener parámetros y dos puntos. Los parámetros son “**variables de entrada**” que pueden cambiar cada vez que se llama a la función. Esto permite a las funciones operar con diferentes datos y producir resultados correspondientes.

Las funciones pueden devolver valores al programa principal o a otras funciones mediante la palabra clave ‘**return**‘. Esto las hace increíblemente versátiles, ya que pueden procesar datos y luego pasar esos datos modificados a otras partes del programa.

```python 
#!/usr/bin/env python3 

def Saludo(name):                      # Asi se define una funcion, a la cual le puede 'pasar' un argumento 
	print(f"\nHola {name}")

Saludo("Omar")                         # Asi mandas a llamar a la funcion y le colocas el argumento que le quieras pasar



def suma(a, b):
	return x + y                      # Return te devuelve un 'solo' resultado 

resultado = suma(2, 8)
print(f"La suma es {resultado}")
# o
print(f"La suma es {suma(9,3)}")       # Puedes llamar a la funcion y te devuelve el resultado sin almacenarlo en una variable 
```


**Ámbito de las Variables (Scope)**
El ámbito de una variable se refiere a la región de un programa donde esa variable es accesible. En Python, hay dos tipos principales de ámbitos:

- **Local**: Las variables definidas dentro de una función tienen un ámbito local, lo que significa que solo pueden ser accesadas y modificadas dentro de la función donde fueron creadas.
- **Global**: Las variables definidas fuera de todas las funciones tienen un ámbito global, lo que significa que pueden ser accesadas desde cualquier parte del programa. Sin embargo, para modificar una variable global dentro de una función, se debe declarar como global.

```python 
#!/usr/bin/env python3 

var = "Soy global"              # La variable global esta en todo el codigo y puede ser utilizada por todas las funciones 

def mi_funcion():
	global var                 # Puedes modificar a la variables global desde una local 
	var = "Soy local y puedo modificar el valor de 'var' de maner global"   # La variable local solo vive dentro de la funcion
	print(var)

mi_funcion()                    # Aqui llamara a la funcion 
print(var)                      # Imprimira el valor de la variable 
```