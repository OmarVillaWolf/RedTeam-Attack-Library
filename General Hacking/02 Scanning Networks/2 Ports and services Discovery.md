# Puertos y servicios 

Tags: #Hacking #NetworkScanning #PortScanning #Nmap 

## Mega ping 

```bash 
# Windows tool 
❯ megaping.exe     # Ejecutamos el programa 

Nota: 
	1. Seleccionar 'IP Scanner', colocar un rango y dar click en 'Start'
	2. Seleccionar 'Port Scanner', colocar la IP del server y dar click en 'Start'
```

## NetScan Tool Pro 

```bash 
# Windows tool 
❯ nstp11demo.exe   # Ejecutamos el programa 

Nota: 
	1. Seleccionar 'Ping Scanner' en 'Manual Tools (all)'
	2. Colocar 'Use Default System DNS', colocar el rango de IP y dar click en 'Start'
	3. Seleccionar 'Port Scanner', colocar la IP del server, aegurarse de tener seleccionado 'TCP Full Connect' y dar click en 'Scan Range of Ports'
```

## SX Tool 

* [Code_ICMP](https://www.iana.org/assignments/icmp-parameters/icmp-parameters.xhtml)

```bash 
# Kali tool 
❯ sx arp IP/24     #  Escaneo ARP para mostrar la MAC Address 
❯ sx arp IP/24 --json | tee arp.cache              # Crear un archivo llamado 'arp.cache'
	❯ cat arp.cache | sx tcp -p 1-65535 <IP>      # Muestras los puertos de la IP 
	❯ cat arp.cache | sx udp --json -p 53 <IP>    # Escaneo UDP a un puerto especifico 

Nota: 
	1. Si muestra un codigo y un tipo '3' quiere decir que es 'Dest. Unreacheable, Port Unreachable', por lo tanto el puerto esta cerrado  
```

## Zenmap 

```bash 
# Windos tool
❯ zenmap.exe     # Ejecutamos el programa 

Nota:
	1. Colocar la IP en 'Target' 
	2. Escoger un tipo de escaneo especifico en 'Profile' o se puede escribir el comando manualmente
	3. En la pestaña de 'Profile' se puede crear un perfil personalizado de escaneo 
	4. En la pestaña de 'Topology' se ve la topología 
	5. En la pestaña de 'Host Details' se miran los detalles 
	6. Si no se especifican los puertos a escanear, Zenmap escanear los 1000 puertos más comunes  

❯ nmap -sT -v -n IP        # Comando manual 
❯ nmap -sS -v IP           # Escaneo 'Stealth Scan/TCP half-open scan'
❯ nmap -sX -v IP           # Escaneo 'Xmas' y funciona en maquinas Linux ya que envia banderas 'FIN, URG y PUSH'
❯ nmap -sM -v IP           # Escaneo 'Maimon' y funciona en maquinas Linux
❯ nmap -sA -v IP           # Escaneo 'ACK flag' y funciona en maquinas Linux
❯ nmap -sU -v IP           # Escaneo 'UDP'
❯ nmap -sV -v IP           # Escaneo 'Versiones'
❯ nmap -sA -v IP.*         # Escaneo 'Agresivo' en toda la red 
```

## Nmap 

```bash 
❯ nmap -p 80,8080,443 -T4 -A -sV IP  # Escaneo para determindar WampServer       
❯ nmap -T4 IP.0/24               # Escaneo de puertos 
❯ nmap -p 88,53,445,636,389 -T4 IP.0/24    # Escaneo para encontrar el 'Domain Controller'
❯ nmap -p 21,22,23,80,3389,3333 --open -T4 -A # El linux es un telnet
❯ nmap -T4 -A IP                 # Host discovery para encontrar info del puertos
❯ nmap -p3389 -sCV IP            # Host discovery para encontrar info del puerto 3389 donde se ve 'NetBios Computer Name, DNS Computer Name' 
❯ nmap -p445 -sCV -A -T4 IP      # Escaneo para encontrar SMB, ademas del FQDN 
❯ nmap -T4 -A IP                 # Escaneo de todos los puertos y encontrar el 'DNS Computer Name' 

❯ nmap -T4 -A -sV IP.0/24        # Identificar la versión de los puertos en cada IP   
```

## Hping 3

```bash 
# Kali tool 
❯ hping3 -A IP -p 80 -c 5 

	# A = Bandera ACK
	# p = Puerto a escanear 
	# c = Numero de paquetes a enviar 
Nota: 
	1. Si sale "DF = Don't fragment" quiere decir que los paquetes no estan fragmentados 


❯ hping3 -8 0-100 -S IP -V  

	# 8 = Modo de escaneo 
	# p = Puertos del 0 al 100
	# V = Verbose 
	# S = Bandera SYN
Nota:
	1. Si el puerto esta abierto responde con un 'S. A. = SYN ACK' 
	2. Si el puerto esta cerrado responde con un 'R. A. = Reset ACK'


❯ hping3 -F -P -U IP -p 80 -c 5       # Escaneo Xmas y funciona en maquinas Linux

	# F, P, U = Bandera FIN,PUSH, URG 

❯ hping3 --scan 0-100 -S IP     # scan = Especificar el rango de puertos a escanear 
❯ hping3 -1 IP -p 80 -c 5       # Escaneo ICMP 

❯ hping3 -1 IP_Subnet --rand-dest -i eth0    # Enviar tráfico aleatorio 
❯ hping3 -2 IP -p 80 -c 5                    # Escaneo UDP 
```