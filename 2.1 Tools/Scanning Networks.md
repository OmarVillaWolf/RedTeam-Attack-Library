# Network Scanning Tools

Tags:  #Scanning #Networks #Netdiscover #Hping3 #Proxychains 

## Técnicas para descubrir hosts

```bash 
1. ARP Ping Scan 
2. UDP Ping Scan
3. ICMP Ping Scan (ICMP Echo Ping 'ICMP Echo Ping Sweep', ICMP Timestamp Ping, ICMP Address Mask Ping)
4. TCP Ping Scan (TCP SYN Ping, TCP ACK Ping)
5. IP Protocol Ping Scan
```

## Herramientas 

```bash
1. Nmap o Zenmap
2. Hping3 
3. Metasploit
4. NetScanTools Pro
5. Angry IP Scanner 
6. SolarWinds
7. SSDP Scan (Metasploit)
```

```bash 
❯ hping3 -1 IP              # Ping ICMP
❯ hping3 -A IP -p 80        # Escaneo ACK en el puerto 80
❯ hping3 -2 IP -p 80        # Escaneo UDP en el puerto 80
❯ hping3 -s IP -p 80 --tcp-timestamp    # A Firewalls and Timestamps
❯ hping3 -8 50-60 -s IP -v              # Escaneo SYN en el puerto 50,60 
❯ hping3 -s IP -a IP -p 22 --flood      
❯ hping3 -a 1.1.1.1 <IP>                # Falsificar la IP de origen para evadir el IDS o Firewall 
	# a = Origen 'Puede no existir'
```

```bash 
❯ netdiscover                # Descubrimiento de la red. Es una herramienta ruidosa 
```

## Proxy tools 

```bash 
❯ Proxy Switcher             # Herramientas GUI
❯ CyberGhost VPN
```

```bash 
# Herramienta de Kali

❯ proxychains  
	❯ nvim /etc/proxychains.conf      # Debemos modificar el archivo de configuracion 
		# socks4 127.0.0.1 9050      # Comentamos esa instrauccion para que no sea considerada 
		http    <IP> 80              # Agregaremos un proxy de 'http'

❯ proxychains firefox <domain.com>     # Abrimos el dominio a traves del proxy 
❯ proxychains nmap -sS -v <IP>         # Utilizar 'Proxychains' para hacer un escaneo nmap 
```

## Anonimizador 

```bash 
❯ https://centralops.net        # En el 'Browser mirror' podemos ver la peticion de la maquina con el server 
```

```bash 
# Anonimizadores web 
❯ https://anonymouse.org        # La version 'http' es gratis, pero la version 'https' es de pago 
```

```bash 
❯ whonix                        # Sistema operativo completo anonimizador
```

```bash 
❯ Alkasir                       # Evadir la censura de un pais 
❯ Tails                         # Sistema operativo para evadir la censura
```