# Librería requests 

Tags: #Libreria-requests

La biblioteca ‘**requests**‘ en Python es una de las herramientas más populares y poderosas para realizar solicitudes HTTP. Su diseño es intuitivo y fácil de usar, lo que la hace una opción preferida para interactuar con APIs y servicios web. 

**Introducción a requests**

‘**requests**‘ es una biblioteca de Python que simplifica enormemente el proceso de enviar solicitudes HTTP. Está diseñada para ser más fácil de usar que las opciones incorporadas en Python, como ‘**urllib**‘, proporcionando una API más amigable.

**Características Principales**

- **Simplicidad y Facilidad de Uso**: Con requests, enviar solicitudes GET, POST, PUT, DELETE, entre otras, se puede realizar en pocas líneas de código. Su sintaxis es clara y concisa.
- **Gestión de Parámetros URL**: Permite manejar parámetros de consulta y cuerpos de solicitud con facilidad, automatizando la codificación de URL.
- **Manejo de Respuestas**: ‘**requests**‘ facilita la interpretación de respuestas HTTP, proporcionando un objeto de respuesta que incluye el contenido, el estado, los encabezados, y más.
- **Soporte para Autenticaciones**: Ofrece soporte integrado para diferentes formas de autenticación, incluyendo autenticación básica, digest, y OAuth.
- **Manejo de Sesiones y Cookies**: Permite mantener sesiones y gestionar cookies, lo cual es útil para interactuar con sitios web que requieren autenticación o mantienen estado.
- **Soporte para SSL**: ‘**requests**‘ maneja SSL (Secure Sockets Layer) y TLS (Transport Layer Security), permitiendo realizar solicitudes seguras a sitios HTTPS.
- **Manejo de Excepciones y Errores**: Proporciona métodos para manejar y reportar errores de red y HTTP de manera efectiva.

**Uso Práctico**

La biblioteca se utiliza ampliamente para interactuar con APIs RESTful, automatizar interacciones con sitios web, y en tareas de scraping web. Sus capacidades para manejar solicitudes complejas y sus características de seguridad la hacen ideal para una amplia gama de aplicaciones, desde scripts simples hasta sistemas empresariales complejos.

## Enviar datos por el método GET

```python 
#!/usr/bin/env python3 

import requests 

response = requests.get("https://google.es")      # Peticion directa con el metodo GET y regresa el codigo de estado
print(f"\n[+] Cosigo de estado: {response.status_code}")     # Observamos el codigo de estado  
print(f"\n[+] Mostrando codigo fuente de la respuesta: ")

with open("index.html", "w") as f:                # Almacena la respuesta 'codigo fuente' en el archivo 'index.html'
	print(response.text)                      
```

```python 
#!/usr/bin/env python3 

import requests 

values = {'key1': 'value1', 'key2': 'value2', 'key3': 'value3'}

response = requests.get("https://httpbin.org/get", params=values)  # Agregamos argumentos a la url los valores de la variable 'values'

print(f"\n[+] URL final: {response.url}\n")   # Muestra la url completa con los paramteros 
print(response.text)
```


## Enviar datos por el método POST

```python 
#!/usr/bin/env python3 

import requests 

payload = {'key1': 'value1', 'key2': 'value2', 'key3': 'value3'}
headers = {'User-Agent': 'my-app/1.0.0'}

response = requests.post("https://httpbin.org/post", data=payload, headers=headers) 

print(response.text)
```

## Enviar cabeceras 

```python 
#!/usr/bin/env python3 

import requests 

headers = {'User-Agent': 'my-app/1.0.0'}

response = requests.get("https://google.es", headers=headers) 

print(response.request.headers)           # Miramos la parte del 'User-Agent'
```