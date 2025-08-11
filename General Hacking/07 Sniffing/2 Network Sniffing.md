# Sniffing de red pasivo

Tags: #Hacking #Sniffing #Wireshark #Ñ

## Wireshark 

```bash 
# Windows Tool 
❯ wireshark &       # Para snifear el tráfico en la interface 

# Filtrar paquetes 
❯ http.request.method eq POST       # Filtrar por el método POST 
❯ tcp.port == 80 and http.request.method == POST    # Filtrar la petcion POST y buscar en HTML Form
❯ tcp.port eq 21              # Filtrar por un puerto en especifico 
❯ tcp.flags.syn == 1          # Filtrar por paquetes 
❯ ip.addr == 1.1.1.1          # Filtrar por la dirección IP y buscar texto en TCP de la IP filtrada
❯ ftp                         # Buscar en FTP 
❯ mqtt                        # Filtrar por el procolo mqtt, buscar en 'Publish Message en Info'. Ahí se encuentra la longitud del mensaje, longitud del tema, tema, el mensaje y la alerta. 
❯ dhcp         # Snifear el tráfico del ataque al protocolo DHCP, donde se encuentra la transacción del ID
❯ icmp         # Snifear el tráfico del protocolo ICMP para encontrar el paquete ID 'Identifier BE'

# DoS (SYN - SYN ACK)
	1. Ir a 'Stadistics > Conversations > IPV4' y mirar el que manda mas paquetes, esto se puede hacer filtrando por 'bytes'
	2. Ir a 'Analyze > Expert information' para identificar el nivel de severidad del ataque 

❯ tcp.flags.syn == 1 and tcp.flags.ack == 0 
❯ tcp.flags.syn == 1
❯ tcp.flags.syn == 1 and tcp.flags.ack == 1

Notas:
	1. Se puede segur la trama en 'Follow > HTTP' 
	2. Se puede segur la trama en 'Follow > TCP' donde se puede aumentar el 'Stream' para ver mas contenido como algun texto
	3. Se puede encontrar y extraer archivos 'Export Objects > HTTP' despues se filtra en 'Content Type' y guardar el archivo de interes para ver si hay contenido en el
	4. Para encontrar comentarios se debe selccionar el paquete y dar click en el icono de la izquierda inferior que es una libreta y una pluma 
	5. Activar la barra de busqueda con 'Ctrl + F' para buscar un string especifico 
```

## Omnipeek Network Protocol Analyzer

```bash 
# Web Tool 
❯ https://www.liveaction.com/products/omnipeek-network-protocol-analyzer/   

Nota:
	1. Dar click en 'Start Trial', hacer el registro para descargar el instalador y al llave de licencia  
	2. Ejecutar el instalador y seleccionar 'Automatic: requires an internet connection' e instalar 
	3. Dar click en 'New Capture', en 'Capture Options' seleccionar el adaptador 
	4. Dar click en la opción 'Start'
```