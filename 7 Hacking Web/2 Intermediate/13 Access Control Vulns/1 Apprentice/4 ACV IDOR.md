# Referencias directas inseguras a objetos

Tags: #AccessControl #BurpSuite 

```bash 
En este lab tras enviar un mensaje desde la pestaña de chat, se accede a la transcripción y se analiza la URL. Se comprueba que los nombres de archivo siguen un patrón incremental ('1.txt, 2.txt, etc.'), por lo que se modifican manualmente para intentar acceder a otras conversaciones.
```

```bash 
# Petición original 
	GET /dowload-transcript/10.txt HTTP/2
	
	
Notas:
	1. Ir modificando el id hasta encontrar contenido de valor 
```