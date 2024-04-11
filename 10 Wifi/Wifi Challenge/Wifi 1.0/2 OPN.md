# Wifi

Tags: #Linux #Wifi #Airmon-ng  #Aircrack #Airodump-ng 

## 05. Access to wifi-guest network

```bash 
# La red a continuación es una OPN = Open Network 

❯ airodump-ng wlan0mon --band abg --manufacturer --wps -w <file>      # Escanear todas las redes y el reultado lo guarda en varios archivos 
```

```bash 
# Para ingresar a una red OPN debemos de usar 'wpa_supplicant' y sirve para la autenticación en redes Wifi 

❯ nano open.conf              # Creamos un file llamado 'open.conf'
	network={ 
	ssid="wifi-guest" key_mgmt=NONE 
	}
# ssid = Nombre de la red OPN 

❯ wpa_supplicant -D nl80211 -i wlan2 -c open.conf   # Nos conectamos a la red OPN con esta herramienta 
	# c = Archivo de la configuración
	# D = Driver Linux (nl80211/cfg80211)
	# i = Interfaz de red inalambrica a usar 
```

```bash 
# Si no podemos conectarnos a la red OPN, es porque tiene un filtrado MAC de lista blanca, por lo que es necesario cambiar nuestra MAC por una MAC de algun cliente conectado a la red. Esto lo hacemos de la siguiente manera:

❯ ifconfig <wlan> down                       # Debemos de dar de baja la Wlan para poder cambiarle la MAC
❯ macchanger -m <00:20:91:da:af:91> <wlan>   # Colocamos la MAC de un cliente conectado a la red OPN
❯ ifconfig <wlan> up                         # Damos de alta la interfaz
❯ macchanger -s <wlan>                       # Mirar la MAC del dispositivo 

# Despues de cambiar la MAC nos conectamos con 'wpa_supplicant' y obtenemos una IP de la siguiente manera:
❯ dhclient <wlan2> -v 
```