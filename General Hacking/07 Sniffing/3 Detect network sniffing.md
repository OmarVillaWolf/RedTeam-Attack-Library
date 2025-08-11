# Detect network sniffing 

Tags: #Hacking #Sniffing 

## Detectar envenenamiento ARP modo promiscuo basado en switch

```bash 
# Windows Tool 
❯ cain.exe          # Ejecutamos el programa para snifear el tráfico y obtener claves 

Nota:
	1. En 'Configure' se puede ocnfigurar el adaptador de red 
	2. Se puede 'Start/Stop' el sniffer con el icono 
	3. Seleccionar la pestaña de 'Sniffer'
	4. En el icono de '+' se puede agregar la opción para que se detecten todas las máquinas de la red 
	5. En la pestaña de 'APR' hacer click en la tabla blanca para activar el icono '+' y poder seleccionar las máquinas para poder hacer 'man-in-the-middle'
	6. Escoger la IP a atacar
	7. Para activar el ataque dar click en el icono 'Start/Stop APR'
	8. Lo siguinetes pasos se hacen con Wireshark y Zenmap
		1. En zenmap colocar 'nmap --script sniffer-detect IP' para detectar se la IP esta en modo promiscuo 


# Kali 
❯ hping3 IP -c 100000      # Generar 100000 paquetes a la IP víctima
```

## Detectar envenenamiento ARP usando Capsa Network Analyzer

```bash 
# Web Tool 
❯ https://www.colasoft.com/download/arp_flood_arp_spoofing_arp_poisoning_attack_solution_with_capsa.php

Nota:
	1. Descargar el 'Trial' y activarlo con la clave de activación
	2. Seleccionar el adaptador de red y dar click en 'Start'


# Kali
❯ habu.arp.poison IPsource IPdest
```