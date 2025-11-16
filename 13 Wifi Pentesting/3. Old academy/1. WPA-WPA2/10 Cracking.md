#  Tools para crackear  

Tags: #Cracking #Pyrit #WPA/WPA2 #Cowpatty #Airolib 

##  Cracking con Pyrit

```bash 
1. Ataque de fuerza bruta a un archivo que contiene un 'handshake' con 'Pyrit'

❯ pyrit -r Captura.cap -e <AP> -i /usr/share/wordlists/rockyou.txt attack_passthrough 
	# r = Archivo de la captura 
	# e = Nombre del AP
	# i = Diccionario a usar 
	# attack = Modo de ataque a usar 
```

##  Cracking con Cowpatty

```bash 
❯ cowpatty -r Captura.cap -f /usr/share/wordlists/rockyou.txt -s <AP> 
	# r = Archivo de la captura 
	# f = Diccionario a usar
	# s = Nombre del AP (SSID)
```

##  Cracking con Airolib

```bash 
1. Esta herramienta crea un diccionario de claves precomputadas 

❯ airolib-ng <passwd-airolib> --import passwd /usr/share/wordlists/rockyou.txt
	# Indicamos un archivo de exportacion, en esta caso lo llamaremos 'passwd-airolib'
	# import =  Indicas que quieres importar (En este caso passwd) y agregas el archivo de passwd por ejemplo rockyou.txt

❯ airolib-ng <passwd-airolib> --import essid essid.lst
	# Indicamos un archivo de exportacion, en esta caso lo llamaremos 'passwd-airolib'
	# import =  Indicas que quieres importar (En este caso essid) y agregas un archivo con los nombres de los APs con extension .lst

❯ airolib-ng <passwd-airolib> --stats              # Miramos los APs en el archivo que hemos creado y las passwd cargadas 
❯ airolib-ng <passwd-airolib> --clean all          # Limpiar el archivo de saltos de linea y errores 
❯ airolib-ng <passwd-airolib> --batch              # Creas el diccionario final 
	kill %%                                       # Lo detenemos si queremos ya que trada mucho y paramos el proceso 


❯ aircrack-ng -r <passwd-airolib> Captura.cap      # Crackear con el archivo anterior 
```
