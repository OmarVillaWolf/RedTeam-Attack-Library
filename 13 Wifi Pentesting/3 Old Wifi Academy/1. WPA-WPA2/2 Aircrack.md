# Comandos Comunes 

Tags: #Aircrack #Airmon-ng #Airodump-ng #WPA/WPA2 

## Comandos Linux 

```bash 
❯ iwconfig                     # Mirar las intrfaces Wifi

❯ macchanger -s <wlan>         # Mirar la MAC del dispositivo 
	# s = Muestras la MAC de la tarjeta de red  
```

## Modo monitor (Aircrack)

```bash 
❯ airmon-ng start <wlan>            # Inicias el modo monitor en la Wlan 
❯ iwconfig                          # Miramos la Wlan en modo monitor 

❯ ifconfig <wlan0mon> up            # Levantamos nuestra interface en modo monitor
❯ ifconfig                          # Miramos la interface levantada 

❯ airmon-ng check kill              # Paramos los procesos para no entrar en conficto con el modo monitor 

❯ airmon-ng stop <wlan0mon>         # Paramos el modo monitor, despues de parar el modo monitor hacemos lo siguiente: 
❯ service network-manager restart   # Para hacer que la tarjeta tenga la configuracion normal que tenia 

❯ ifconfig <wlan0mon> down          # Debemos de dar de baja la Wlan0mon para poder cambiarle la MAC y despues darla de alta
# Para los ataques Wifi debemos de falsificar la MAC colocando un OUI valido como el de 'National Security Agency = 00:20:91' y el resto se inventa, el cual debe de estar en hexadecimal
❯ macchanger --mac=00:20:91:da:af:91 <wlan0mon>
```

## Captura de paquetes 

```bash 
❯ airodump-ng <wlan0mon>                          # Iniciamos la captura de paquetes (Informacion AP, Clients)

❯ airodump-ng -c 1 <wlan0mon>                     # Filtramos por un canal especifico
❯ airodump-ng -c 1 --essid <name> <wlan0mon>      # Filtramos por el ESSID (Nombre) en el canal 1
❯ airodump-ng -c 1 --bssid <MAC> <wlan0mon>       # Filtramos por el BSSID (MAC) en el canal 1

❯ airodump-ng -c 1 --bssid <MAC> <wlan0mon> -w Captura # Guardaremos la captura de datos en varios archivos, pero el que nos interesa (.cap), la passwd esta cifrada
```