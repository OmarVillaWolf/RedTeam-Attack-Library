# Validación del inicio de la ruta

Tags: #DirectoryPathTraversal #BurpSuite 

```bash 
En este lab la aplicación intenta validar que las rutas comiencen con '/var/www/images/', pero no restringe adecuadamente lo que viene después. Aprovechamos esto enviando una ruta como '/var/www/images/https://web.com/../etc/passwd', que tras la resolución final accede al archivo sensible fuera del directorio permitido.
```

```bash 
	?file=/var/www/images/../../../etc/passwd     # Mirar un archivo en específico
```