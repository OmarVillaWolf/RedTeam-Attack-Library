# Python 2 vs Python 3

Tags: #Python3 #Python2

**Python 2 vs Python 3:**

- **Sintaxis de print**: En Python 2, ‘print’ es una declaración, mientras que en Python 3, ‘print()’ es una función, lo que requiere el uso de paréntesis.
- **División de enteros**: Python 2 realiza una división entera por defecto, mientras que Python 3 realiza una división real (flotante) por defecto.
- **Unicode**: Python 3 usa Unicode (texto) como tipo de dato por defecto para representar cadenas, mientras que Python 2 utiliza ASCII.
- **Librerías**: Muchas librerías populares de Python 2 han sido actualizadas o reescritas para Python 3, con mejoras y nuevas funcionalidades.
- **Soporte**: Python 2 llegó al final de su vida útil en 2020, lo que significa que ya no recibe actualizaciones, ni siquiera para correcciones de seguridad.

**PIP2 vs PIP3 (Pip Installs Packages):**

- **Gestión de paquetes**: PIP2 y PIP3 son herramientas que permiten instalar paquetes para Python 2 y Python 3, respectivamente. Es importante usar la versión correcta para garantizar la compatibilidad con la versión de Python que estés utilizando.
- **Comandos de instalación**: El uso de pip o pip3 antes de un comando determina si el paquete se instalará en Python 2 o Python 3. Algunos sistemas operativos pueden requerir especificar pip2 o pip3 explícitamente para evitar ambigüedades.
- **Ambientes virtuales**: Es una buena práctica usar ambientes virtuales para mantener separadas las dependencias de proyectos específicos y evitar conflictos entre versiones de paquetes para Python 2 y Python 3.

```bash 
❯ apt install python2 python3                              # Instalar Python 2 y 3 en Linux
❯ apt install python3-pip                                  # Instalar pip3

❯ wget https://bootstrap.pypa.io/pip/2.7/get-pip.py        # Instalar pip2
❯ sudo python2.7 get-pip.py

❯ pip3 install pwntools                                    # Instalación de Pwntools Python3
❯ pip2 install pwntools                                    # Instalación de Pwntools Python2
	❯ pip2 install pathlib2                               # Instalación del complemento para usar las librerias de Pwntools
```