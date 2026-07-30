# Secuestro de la biblioteca de objetos compartidos enlazados dinámicamente

Tags: #Linux #SecuestroBiblioteca #Escalada #Root #Privilegios 

Las **bibliotecas compartidas** son archivos que contienen funciones y recursos utilizados por múltiples programas. Cuando un programa requiere una función de una biblioteca compartida, el sistema operativo busca la biblioteca y enlaza dinámicamente la función requerida durante la ejecución del programa. Sin embargo, si el sistema no encuentra la biblioteca en las rutas predeterminadas, puede buscarla en otros directorios.

Un atacante puede aprovechar esta situación creando una **biblioteca compartida maliciosa** con el mismo nombre que la biblioteca legítima y colocándola en un directorio donde el sistema la buscará. Cuando el programa intenta cargar la biblioteca, el sistema cargará la versión maliciosa en lugar de la legítima, permitiendo al atacante ejecutar código malicioso con los privilegios del programa víctima.

En esta clase, analizaremos cómo se lleva a cabo el secuestro de bibliotecas de objetos compartidos enlazados dinámicamente y cómo identificar situaciones en las que esta técnica puede ser aplicada.

A continuación, se os comparte una de las herramientas que utilizamos en esta clase para analizar la ejecución de un programa escrito en C/C++:

- **Herramienta Uftrace**: [https://github.com/namhyung/uftrace](https://github.com/namhyung/uftrace)

Asimismo, se os comparte el enlace directo a la plataforma **AttackDefense**, donde estaremos resolviendo un reto que involucra esta misma temática:

- **AttackDefense**: [https://attackdefense.com](https://attackdefense.com/)
