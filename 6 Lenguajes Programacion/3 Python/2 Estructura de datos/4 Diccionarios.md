# Diccionarios 

Tags: #Python3 

**Características de los Diccionarios**
- **Desordenados**: Los elementos en un diccionario no están ordenados y no se accede a ellos mediante un índice numérico, sino a través de claves únicas.
- **Dinámicos**: Se pueden agregar, modificar y eliminar pares clave-valor.
- **Claves Únicas**: Cada clave en un diccionario es única, lo que previene duplicaciones y sobrescrituras accidentales.
- **Valores Accesibles**: Los valores no necesitan ser únicos y pueden ser de cualquier tipo de dato.

**Operaciones con Diccionarios**
Durante la clase, exploraremos cómo realizar operaciones básicas y avanzadas con diccionarios:

- **Agregar y Modificar**: Cómo agregar nuevos pares clave-valor y modificar valores existentes.
- **Eliminar**: Cómo eliminar pares clave-valor usando del o el método ‘**pop()**‘.
- **Métodos de Diccionario**: Utilizar métodos como ‘**keys()**‘, ‘**values()**‘, y ‘**items()**‘ para acceder a las claves, valores o ambos en forma de pares.
- **Comprensiones de Diccionarios**: Una forma elegante y concisa de construir diccionarios basados en secuencias o rangos.

**Uso de Diccionarios en Python**
- **Almacenamiento de Datos Estructurados**: Ideales para almacenar y organizar datos que están relacionados de manera lógica, como una base de datos en memoria.
- **Búsqueda Eficiente**: Los diccionarios son altamente optimizados para recuperar valores cuando se conoce la clave, proporcionando tiempos de búsqueda muy rápidos.
- **Flexibilidad**: Pueden ser anidados, lo que significa que los valores dentro de un diccionario pueden ser otros diccionarios, listas o cualquier otro tipo de dato.

```python  
#!/usr/bin/env python3 

diccionario = {"nombre": "Omar", "edad":"29", "ciudad":'CDMX'}   # Así creamos un diccionario  {'clave' : 'valor'}
print(diccionario)
print(diccionario["edad"])                      # Mostramos el valor de la clave 

diccionario["nombre"] = "Juan"                  # Le cambias el valor a una clave en especifico, si no existe la clave la agrega

del diccionario["edad"]                         # Borras la clave y por ende su valor del diccionario 
diccionario.clear()                             # Eliminas las claves y sus valores en el diccionario 

print(diccionario.get("nombre", "No encontrado")) # Si encuentra la llave, muestra su valor. Si no la encuentra muestra 'No encontrado'

for key, value in diccionario.items():           # Muestra la clave y valor
	print(f"Para la clave {key} tendríamos el valor {value}")


for i in diccionario.keys():                     # Solo muestra los nombres de las claves
for i in diccionario.values():                   # Muestra los valores de las claves 
	print(i)

diccionario2 = {"animal":"perro", "profesion": "estudiante"}
diccionario.update(diccionario2)                 # Actualizamos el primer diccionario 


# Diccionario anidado 
dict = {
	   "nombre" : "Omar",
	   "hobbies" : {"primero":"Gym", "segundo":"Futbol"},
	   "edad" : 28
} 
print(dict["hobbies"])                        # Muestras el valor de la clave 'hobbies'
print(dict["hobbies"]["primero"])             # Dentro de la clave 'hobbies' muestras el valor de 'primero'
```