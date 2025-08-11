# Website Footprinting 

Tags: #Hacking #Footprinting #Website #Crawling

```bash 
# Kali tool 
❯ python3 photon.py -u http://www.domain.com      # Buscar info de un sitio web 'crawling'

	# u = Ingresar la URL
	# h = Panel de ayuda 

Nota: Esta tool nos crea un directorio con la recopilación externa, interna y scripts

❯ python3 photon.py -u http://www.domain.com -i 3 -t 200 --wayback 

	# i = Especifica el nivel de 'Crawl' 
	# t = Peticiones simultaneas 
	# wayback = Busca en el historico 'Wayback machine' (https://web.archive.org/)
```