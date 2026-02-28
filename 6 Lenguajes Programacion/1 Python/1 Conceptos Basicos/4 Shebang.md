# Shebang

Tags: #Python3 

**Shebang en Python:**

El shebang es una línea que se incluye al principio de un script ejecutable para indicar al sistema operativo con qué intérprete debe ejecutarse el archivo. En los scripts de Python, el shebang común es:

- **#!/usr/bin/env python3**

Esta línea le dice al sistema que utilice el entorno (**env**) para encontrar el intérprete de Python 3 y ejecutar el script con él. Es fundamental para asegurar que el script se ejecute con Python 3 en sistemas donde Python 2 todavía está presente.

**Convenios en Python:**

Los convenios de codificación son un conjunto de recomendaciones que guían a los desarrolladores de Python para escribir código claro y consistente. El más conocido es ‘**PEP 8**‘, que abarca:

- **Nombres de Variables**: Utilizar ‘**lower_case_with_underscores**‘ para nombres de variables y funciones, ‘**UPPER_CASE_WITH_UNDERSCORES**‘ para constantes, y ‘**CamelCase**‘ para clases.
- **Longitud de Línea**: Limitar las líneas a 79 caracteres para código y 72 para comentarios y docstrings.
- **Indentación**: Usar 4 espacios por nivel de indentación.
- **Espacios en Blanco**: Seguir las prácticas recomendadas sobre el uso de espacios en blanco, como no incluir espacios adicionales en listas, funciones y argumentos de funciones.
- **Importaciones**: Las importaciones deben estar en líneas separadas y agrupadas en el siguiente orden: módulos de la biblioteca estándar, módulos de terceros y luego módulos locales.
- **Compatibilidad entre Python 2 y 3**: Aunque Python 2 ha llegado al final de su vida útil, algunos convenios pueden seguirse para mantener la compatibilidad.

```python
#!/usr/bin/env python3

if __name__ == '__main__':     #  Creamos una funcion para saber el modulo principal y se usa para tener un codigo mas 'estructurado'
	print('Hola mundo')
```

```python
#!/usr/bin/env python3

def comprobacionEstadoApi():    # En funciones se aplica el 'lowerCamelCase'
class TwitterApi:               # En clases se aplica el 'UpperCamelCase'

def comprobar_estado_api():     # Para Python lo recomendable es usar 'Snake_Case' (Recomendacion del PEP8)

# Para las constantes podemos usar 'SCREAMING_SNAKE_CASE'
	VERSION_API = 1
	URL_API = "https://google.com"
```