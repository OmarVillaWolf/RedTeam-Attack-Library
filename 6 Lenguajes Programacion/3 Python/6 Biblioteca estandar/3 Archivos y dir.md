# Manejo de archivos y directorios 

Tags: #Python3 #Libreria-os

La librería ‘**os**‘ y el módulo ‘**shutil**‘ en Python son herramientas fundamentales para interactuar con el sistema de archivos, especialmente en lo que respecta a la creación y eliminación de archivos y directorios.

Aquí tienes una descripción detallada de ambas:

**Librería os**
- **Funcionalidades Básicas**: ‘os’ proporciona una interfaz rica y variada para interactuar con el sistema operativo subyacente. Permite realizar operaciones como la creación y eliminación de archivos y directorios, así como la manipulación de rutas y el manejo de la información del sistema de archivos.

**Creación y Eliminación de Archivos y Directorios**
- **Creación de Directorios**: Utilizando ‘**os.mkdir()**‘ u ‘**os.makedirs()**‘, se pueden crear directorios individuales o múltiples directorios (y subdirectorios) respectivamente.
- **Eliminación**: ‘**os.remove()**‘ se usa para eliminar archivos, mientras que ‘**os.rmdir()**‘ y ‘**os.removedirs()**‘ permiten eliminar directorios y directorios con subdirectorios, respectivamente.
- **Gestión de Rutas**: La sublibrería ‘**os.path**‘ es crucial para manipular rutas de archivos y directorios, como unir rutas, obtener nombres de archivos, verificar si un archivo o directorio existe, etc.

**Módulo shutil**
- **Operaciones de Alto Nivel**: Mientras que os se enfoca en operaciones básicas, ‘**shutil**‘ proporciona funciones de nivel superior, más orientadas a tareas complejas y operaciones en lotes.
- **Copiar y Mover Archivos y Directorios**: ‘**shutil**‘ es especialmente útil para copiar y mover archivos y directorios. Funciones como ‘**shutil.copy()**‘, ‘**shutil.copytree()**‘, y ‘**shutil.move()**‘ facilitan estas tareas.
- **Eliminación de Directorios**: Aunque ‘**os**‘ puede eliminar directorios, ‘**shutil.rmtree()**‘ es una herramienta más poderosa para eliminar un directorio completo y todo su contenido.
- **Manejo de Archivos Temporales**: ‘**shutil**‘ también ofrece funcionalidades para trabajar con archivos temporales, lo que es útil para operaciones que requieren almacenamiento temporal de datos.


```python 
#!/usr/bin/env python3 

import os

if os.path.exists("mi_archivo.txt")             # Saber si un archivo existe o no 
	print(f"\n[+] El archivo existe\n")
else: 
	print(f"\n[+] El archivo no existe\n")
```

```python 
#!/usr/bin/env python3 

import os

if not os.path.exists("mi_directorio")         
	os.mkdir("mi_directorio")                  # Si no existe el directorio, lo crea
``` 

```python 
#!/usr/bin/env python3 

import os

if not os.path.exists("mi_directorio/mi_subdirectorio")     
	os.makedirs("mi_directorio/mi_subdirectorio")         # Crea un dir y su sub-dir
```

```python 
#!/usr/bin/env python3 

import os

print(f"Listando todos los recursos del directorio actual de trabajo:\n")    
recursos = os.listdir()           

for recurso in recursos:        # Listamos todos los recursos
	print(recurso)
```

```python 
#!/usr/bin/env python3 

import os

if os.path.exists("file1.txt")       
	os.remove("file1.txt")          # Borrar un archivo
```

```python 
#!/usr/bin/env python3 

import os

if os.path.exists("mi_directorio")   
	os.rmdir("mi_directorio")        # Borrar un directorio pero el este debe de estar vacio
```

```python 
#!/usr/bin/env python3 

import os
import shutil

if os.path.exists("mi_directorio")   
	shutil.rmtree("mi_directorio")    # Borra toda la esctructura del directorio 
```

```python 
#!/usr/bin/env python3 

import os

if os.path.exists("file2.txt")   
	os.rename("file2.txt", "cambiado.txt")  # Renombras un archivo, primero va el 'origen' y despues el 'destino'
```

```python 
#!/usr/bin/env python3 

import os

if os.path.exists("/etc/passwd")   
	tamaño = os.path.getsize("/etc/passwd")        # Obtener el tamaño de un archivo 

print(f"El archivo /etc/passwd pesa {tamaño} bytes")
```

```python 
#!/usr/bin/env python3 

import os

ruta = os.path.join("mi_directorio", "mi_archivo.txt")
directorio, archivo = os.path.split(ruta)

print(f"\n[+] Ruta: {ruta}")
print(f"\n[+] Archivo: {archivo}, Directorio: {directorio}")
```
