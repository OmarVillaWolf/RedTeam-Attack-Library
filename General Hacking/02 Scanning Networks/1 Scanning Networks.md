## Nmap 

Tags: #Hacking #NetworkScanning #Nmap #AngryIPScanner 

```bash 
# Kali tool 
❯ nmap -sn -T4 IP.0/24  # Escanear los host vivos 
❯ nmap -sn -PR <IP>     # ARP ping scan 
❯ nmap -sn -PU <IP>     # UDP ping scan 
❯ nmap -sn -PE <IP>     # ICMP ECHO ping scan  
❯ nmap -sn -PP <IP>     # ICMP timestamp ping scan 
❯ nmap -sn -PM <IP>     # ICMP Address Mask ping scan 
❯ nmap -sn -PS <IP>     # TCP SYN ping scan
❯ nmap -sn -PA <IP>     # TCP ACK ping scan 
❯ nmap -sn -PO <IP>     # IP protocol ping scan (Este usa distintos protocolos)
```

## Angry IP Scanner

```bash 
# Windows tool 
❯ Angry IP Scanner      # Ejecutar la herramienta 

Nota:
	1. Colocar el rango de IPs
	2. En 'Preferences' en la pestaña de 'Scanning' en la parte de 'Pinging method' colocar 'Combined UDP + TCP'
	3. En 'Preferences' en la pestaña de 'Display' en la parte de 'Display in the results list' seleccionar 'Alive hosts' 
	4. Dar click en 'start' 


# Más tools
❯ https://www.solarwinds.com
❯ https://www.netscantools.com
❯ https://www.colasoft.com
❯ https://www.pingtester.net 
❯ https://www.manageengine.com 
```