# Scan beyond IDS and Firewall 

Tags: #Hacking #NetworkScanning #IDS #Firewall 

```bash 
Técnicas de evasión

1. Packet Fragmentation
2. Source Routing
3. Source Port Manipulation 
4. IP Address Decoy 
5. IP Address Spoofing 
6. Creating Custom Packets
7. Randomizing Host Order
8. Sending bad Checksums
9. Proxy Servers
10. Anonymizers 
```

## Nmap

```bash 
# Kali tool 
❯ nmap -f IP       # Fragmentación de paquetes y ver los puertos abiertos 

❯ nmap -g 80 IP    # Escanear con un puerto de origen y ver los puertos abiertos
	# g = Seleccionar el puerto de origen 

❯ nmap -mtu 8 IP   # Escaneo 'mtu = maximum transmission unit' con el valor de 8 y ver los puertos abiertos 

❯ nmap -D RND:10 IP 
	# D = Decoy 'señuelo'
	# RND = Genera 10 IPs aleatorias y no reservadas 

❯ nmap -sT -Pn --spoof-mac 0 IP
	# sT = Escaneo TCP full connect 
	# Pn = Saltar los descubrimientos de host 
	# spoof-mac = Representa una MAC Address aleatoria 
```

## Colasoft Packet Builder 2.0 

```bash 
# Windows Tool 
❯ Colasoft Packet Builder 2.0   # Ejecutamnos el programa 

Nota: 
	1. Seleccionar el adaptador en 'Adapter' 
	2. Seleccionar 'Add' para crear un paquete el cual puede ser 'ARP Packet' y 'Data Time = 0.1seg'
	3. Dar click en 'Send' para enviar el paquete
	4. Saldrá un nuevo menú en donde se selecciona la casilla de 'Burst Mode'
	5. Dar click en 'Start' para iniciar el envio 
```

## Hping3 

```bash 
# Kali Tool 
❯ hping3 IP --udp --rand-source --data 500 
	# udp = Enviar paquetes UDP
	# rand-source = Origen de los paquetes sea aleatorio 
	# data = Especifica el tamaño del paquete 

❯ hping3 -S IP -p 80 -c 5 
	# S = Enviar un paquete TCP SYN 
	# c = Numero de paquetes a enviar 

❯ hping3 IP --flood    # Efectuar un TCP flooding 
```