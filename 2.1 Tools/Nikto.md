# Nikto

Tags: #Nikto #Escaneo 

Esta herramienta sirve para escanear las vulnerabilidades web. Es bueno si lo usas en CTF como HTB, etc... ya que como es un escaner de vulnerabilidades, podría tener problemas al momento de escanear una web y podría ser bloqueado por el firewall si es detectado.

```bash
❯ nikto -h http://IP/            # Escaneo de vulnerabilidades web 
	# h = host 

❯ nikto -h http://IP/index.php?page=arbitrary-file-inclusion.php -Tuning 5 -Display V -o Nikto.html -Format html
	# Tuning 5 = Recuperación remota de archivos: internal web root
	# Display V = Salida detallada
	# o = Output hacia un archivo 
	# Format = Formato del archivo de salida
```

```bash 
❯ nikto -h http://IP/ -ssl            # Muestra info sobre el SSL
```

```bash 
❯ nikto -h niktoscan.txt              # Escanear multiples IPs o dominios dentro de un archivo de texto
```

```bash 
❯ nikto -h http://IP/ -Cgidirs+       # Escanear los directorios '/cgi'
```

```bash 
❯ nikto -h http://IP/ -f txt -o niktoscan.txt     # Guardar el escaneo en un archivo de texto 
```