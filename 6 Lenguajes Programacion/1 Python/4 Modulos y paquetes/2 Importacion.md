# Importación y uso de módulos 

Tags: #Python3 

La importación y uso de módulos es una técnica fundamental en Python que permite la modularidad y la reutilización del código. Los módulos son archivos de Python con extensión ‘**.py**‘ que contienen definiciones de funciones, clases y variables que se pueden utilizar en otros scripts de Python.

**Importación de Módulos**
La declaración ‘**import**‘ es usada para incluir un módulo en el script actual. Cuando importas un módulo, Python busca ese archivo en una lista de directorios definida por ‘**sys.path**‘, la cual incluye el directorio actual, los directorios listados en la variable de entorno ‘**PYTHONPATH**‘, y los directorios de instalación por defecto.

**Uso de Módulos**
Una vez que un módulo es importado, puedes hacer uso de sus funciones, clases y variables, utilizando la sintaxis ‘**nombre_del_módulo.nombre_del_elemento**‘. Esto es esencial para la organización del código, ya que permite acceder a código reutilizable sin necesidad de duplicarlo.

**Importación con Alias**
A veces, por conveniencia o para evitar conflictos de nombres, puedes querer darle a un módulo un alias al importarlo usando la palabra clave ‘**as**‘: ‘**import modulo as alias**‘.

Esto te permite acceder a los componentes del módulo usando el alias en lugar del nombre completo del módulo.

**Importación Específica**
Si solo necesitas una o varias funciones específicas de un módulo, puedes importarlas directamente usando ‘**from modulo import funcion**‘. Esto permite no tener que prefijar las funciones con el nombre del módulo cada vez que se llaman. Además, puedes importar todas las definiciones de un módulo (aunque no es una práctica recomendada) usando ‘**from modulo import ***‘.

**Módulos de la Biblioteca Estándar**
Python viene con una biblioteca estándar extensa que ofrece módulos para realizar una variedad de tareas, desde manipulación de texto, fecha y hora, hasta acceso a internet y desarrollo web. Familiarizarse con la biblioteca estándar es crucial para ser un programador eficiente en Python.

**Módulos de Terceros**
Además de la biblioteca estándar, hay una amplia gama de módulos de terceros disponibles que puedes instalar y utilizar en tus programas. Estos módulos a menudo se instalan utilizando herramientas de gestión de paquetes como ‘**pip**‘.


```python 
#!/usr/bin/env python3 

import math as m                  # Puedes importar el modulo con otro nombre para que sea mas practico 
from math import sqrt as raiz     # Importamos la funcion con otro nombre 

print(m.sqrt(49))
print(raiz(49))
```

```python 
#!/usr/bin/env python3 

import os                 # Importamos el modulo 'os' que por medio de la funcion 'system' se puede ejecutar comandos

os.system("whoami")       # Si el comando es exitoso nos regresa un codigo de estado '0' 
```