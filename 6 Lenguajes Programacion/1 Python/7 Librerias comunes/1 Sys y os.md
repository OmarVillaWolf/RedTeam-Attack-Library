# Librería os y sys

Tags: #Python3 #Libreria-os #Libreria-sys

Las bibliotecas ‘**os**‘ y ‘**sys**‘ de Python son herramientas esenciales para cualquier desarrollador que busque interactuar eficazmente con el sistema operativo y gestionar el entorno de ejecución de sus programas. Estas bibliotecas proporcionan una amplia gama de funcionalidades que permiten una mayor flexibilidad y control en el desarrollo de software.

**Biblioteca os**

La biblioteca ‘**os**‘ en Python es una herramienta poderosa para interactuar con el sistema operativo. Proporciona una interfaz portátil para usar funcionalidades dependientes del sistema operativo, lo que significa que los programas pueden funcionar en diferentes sistemas operativos sin cambios significativos en el código. Algunas de sus capacidades incluyen:

- **Manipulación de Archivos y Directorios**: Permite realizar operaciones como crear, eliminar, mover archivos y directorios, y consultar sus propiedades.
- **Ejecución de Comandos del Sistema**: Facilita la ejecución de comandos del sistema operativo desde un programa Python.
- **Gestión de Variables de Entorno**: Ofrece funciones para leer y modificar las variables de entorno del sistema.
- **Obtención de Información del Sistema**: Proporciona métodos para obtener información relevante sobre el sistema operativo, como la estructura de directorios, detalles del usuario, procesos, etc.

**Biblioteca sys**

La biblioteca ‘**sys**‘ es fundamental para interactuar con el entorno de ejecución del programa Python. A diferencia de ‘**os**‘, que se centra en el sistema operativo, ‘**sys**‘ está más orientada a la interacción con el intérprete de Python. Sus principales usos incluyen:

- **Argumentos de Línea de Comandos**: Permite acceder y manipular los argumentos que se pasan al programa Python desde la línea de comandos.
- **Gestión de la Salida del Programa**: Facilita el control sobre la salida estándar (**stdout**) y la salida de error (**stderr**), lo cual es esencial para la depuración y la presentación de resultados.
- **Información del Intérprete**: Ofrece acceso a configuraciones y funcionalidades relacionadas con el intérprete de Python, como la versión de Python en uso, la lista de módulos importados y la gestión de la ruta de búsqueda de módulos.

## Librería OS

```python 
#!/usr/bin/env python3 

import os 

pwd = os.getcwd()           # Obtenemos la ruta absoluta de trabajo 
files = os.listdir(pwd)     # Lista los directorios y archivos 
os.mkdir("mi_directorio")   # Creamos un dir llamado "mi_directorio"

print(f"Directorio actual de trabajo: {pwd}\n")
print(f"Listando los archivos existentes en: {pwd}\n")

for file in files:
	print(file)

print(f"Existe el archivo 'mi_archivo'? -> {os.path.exists('mi_archivo.txt')}") # Saber si existe un archivo o no

value = os.getenv('KYTTY_INSTALATION_DIR')       # Obtener el valor de una variable de entorno
print(f"Valor de la variable de entorno: {value}")
```


## Librería SYS

```python 
#!/usr/bin/env python3 

import sys 

print(f"\n[+] Nombre del script" {sys.argv[0]}")
	
	# sys.argv[] = Indicamos que elemento queremos listar a nivel posicional  
	# 0 = Referencia al propio script 'nombre'
	# 1 = Referencia al primer argumento que le pasamos al script

print(f"[+] Total de argumentos que se le estan pasando al programa: {len(sys.argv)}")
print(f"[+] Mostrando el primer argumento: {sys.argv[1]}")
print(f"[+] Mostrando el segundo argumento: {sys.argv[2]}")
print(f"[+] Mostrando todos los argumento: {', '.join(argument for argument in sys.argv)}")  # Separa y muestra todos los argumentos 

print(f"\n[+] Mostrando las rutas de Python: \n")   # Ruta de Python
for element in sys.path:
	 print(element)

print(f"[+] Saliendo con un codigo de estado 1 (no exitoso)")
sys.exit(1) # Muestra el codigo de estado '1 = No exitoso, 0 = Exitoso'
```

```bash 
❯ python3 test.py text hola.txt    # Ejecutamnos el programa agregandole argumentos 
```