# Enumeración y explotación de SQUID Proxies

Tags: #SQUID #OWASP #Explotacion 

El **Squid Proxy** es un servidor web **proxy-caché** con licencia **GPL** cuyo objetivo es funcionar como **proxy** de la red y también como zona caché para almacenar páginas web, entre otros. Se trata de un servidor situado entre la máquina del usuario y otra red (a menudo Internet) que actúa como protección separando las dos redes y como zona caché para acelerar el acceso a páginas web o poder restringir el acceso a contenidos.

Es decir, la función de un servidor proxy es centralizar el tráfico de una red local hacia el exterior (Internet). Sólo el equipo que incorpora el servicio proxy debe disponer de conexión a Internet y el resto de equipos salen a través de él:

[![SQUID.png](https://i.postimg.cc/MGdJ8pPw/SQUID.png)](https://postimg.cc/3y4cXYQf)

Ahora bien, puede darse el caso en el que un servidor Squid Proxy se encuentre **mal configurado**, permitiendo en consecuencia a los atacantes recopilar información de dispositivos a los que normalmente no deberían tener acceso.

Por ejemplo, en este tipo de situaciones, un atacante podría ser capaz de realizar peticiones a direcciones IP internas pasando sus consultas a través del Squid Proxy, pudiendo así realizar un escaneo de puertos contra determinados servidores situados en una **red interna**.

Para ello, simplemente podríamos probar a hacer uso de extensiones de navegador como **FoxyProxy** o desde consola haciendo uso del comando ‘**curl**‘:

➜  `curl --proxy http://10.10.11.131:3128 http://<ip>:<port>`

En el mejor de los casos, si la conexión no requiere de autenticación, podríamos llevar a cabo una enumeración de puertos en servidores internos concretos, siendo necesario sustituir **❮port❯** por el puerto deseado que se desea enumerar del servidor **❮ip❯** correspondiente. En caso de requerir autenticación, si el atacante dispone de las credenciales, estas podrían ser especificadas haciendo uso del parámetro ‘**-u**‘.

Todo esto es posible debido a que el proxy actúa como intermediario entre la red **local** y la **externa**, lo que en parte permite el acceso a ciertos recursos internos que normalmente no estarían disponibles desde el exterior.

Sin embargo, es importante tener en cuenta que el acceso a estos recursos a través del proxy puede estar restringido por políticas de seguridad, autenticación u otros mecanismos de control de acceso. Además, si el proxy está configurado correctamente, es probable que no permita el acceso a recursos internos desde el exterior, **incluso si se está pasando a través de él**.

Una de las herramientas que se suelen emplear para enumerar puertos de un servidor concreto pasando por el Squid Proxy es **spose**.

Spose es una herramienta de escaneo de puertos diseñada específicamente para trabajar a través de servidores Squid Proxy. Esta herramienta permite a los atacantes buscar posibles servicios y puertos abiertos en una red interna “protegida” por un servidor Squid Proxy.

A continuación, se proporciona el enlace directo a esta herramienta:

-   **Herramienta Spose**: [https://github.com/aancw/spose](https://github.com/aancw/spose)

Por otro lado, os compartimos también el enlace de descarga de la máquina **SickOs** de Vulnhub:

-   **Máquina SickOs 1.1**: [https://www.vulnhub.com/entry/sickos-11,132/](https://www.vulnhub.com/entry/sickos-11,132/)

Adicionalmente, en esta clase aprovecharemos la ocasión para desarrollar una pequeña herramienta en Python, la cual similar a ‘spose’, nos permitirá enumerar puertos abiertos en un servidor pasando a través del Squid Proxy.


## Comandos 

```bash
❯ nmap -p80 <IP> -T5 -v -n                     # Miraremos si el puerto 80 esta filtrado 
```

```bash
❯ curl http://<IP> --proxy http://<IP>:3128    # Implementaremos un proxy con el curl, se podria decir que pasamos por el SQUID Proxy
```

Debemos de agregar un SQUID Proxy a nuestro 'Foxy Proxy'. Esto nos ayudara a que cuando queramos ver una web y no nos la deje ver porque tiene un SQUID Proxy al momento de activarlo en Foxy Proxy ya podríamos ver el contenido de dicha Web.

Así podremos descubrir directorios en un SQUID Proxy
```bash
# Ataque de Fuerza Bruta a un SQUID Proxy para descubrir directorios 
❯ gobuster dir -u http://<IP>/ --proxy http://<IP>:3128 -w /usr/share/seclists/Discovery/Web-Content/discovery-list-2.3-medium.txt -t 20 

	# t = Numero de peticiones al mismo tiempo
	# w = Path del directorio a usar 
	# proxy = Colocar el SQUID Proxy
	# Si nod debemos de autenticar, debemos colocar en la segunda url eso: http://admin:passwd@<IP>:3128
```

Enumerar los puertos abiertos pasando por el SQUID Proxy con Python
```python
# Primero nos debemos crear un archivo python 

❯ nvim Squid_Proxy_port_discovery.py

#!/usr/bin/python3

import requests
import signal
import sys

def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)
    
# Ctrl + c
signal.signal(signal.SIGINT, def_handler)

# Variables globales
main_url = "http://localhost"
squid_proxy = {'http': 'http://192.168.68.1:3128'}

def portDiscovery():
	puertos_comunes = { 20, 21, 22, 23, 25, 53, 67, 68, 69, 80, 110, 119, 123, 135, 137, 138, 139, 143, 161, 162, 179, 389, 443, 445, 465, 514, 515, 587, 636, 993, 995, 1080, 1433, 1434, 1723, 3306, 3389, 5060, 5222, 5223, 5900, 5901, 5984, 6379, 8080, 8443, 8888, 9200, 9300, 11211, 27017}
	
	for tcp_port in puertos_comunes:
		r = requests.get(main_url + ':' + str(tcp_port), proxies=squid_proxy)
		if r.status.code != 503:
			print("\n[+] Port " + str(tcp_port) + " - OPEN")

if __name__ == '__main__':
	portDiscovery()
```