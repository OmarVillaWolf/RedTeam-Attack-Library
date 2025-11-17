# Obtener la passwd de la captura 

Tags: #Hash #John #BruteForce #Attack #WPA/WPA2 #Aircrack 

##  Extracción del Hash en el Handshake con Aircrack

```bash 
1. Crear un archivo con la extension '.hccap'

❯ aircrack-ng -J <miCaptura> Captura.cap     # Creas un archivo con hccap para despues extraer el hash
	# J = Crea un archivo Hashcat en este caso con el nombre de 'miCaptura' y le agrega la extension '.hccap'
```

```bash 
2. Extraer el 'Hash'

❯ hccap2john miCaptura.hccap > miHash        # Extraemos el hash de la captura anterior y lo exportamos a un archivo, ahi tendremos la passwd encriptada de la red inalambrica 
```

```bash 
3. Romper la passwd para obtenerla en texto plano 

❯ john --wordlist==/usr/share/wordlists/rockyou.txt miHash    # Hacemos un ataque de fuerza bruta al hash para obtener la passwd 
❯ john --show miHash                                          # Muestra la passwd en lugar del hash 
```

## Obtener la passwd con Aircrack 

```bash 
❯ aircrack-ng -w /usr/share/wordlists/rockyou.txt Captura.cap  # Fuerza bruta al archivo de captura.cap que contiene el handshake 
	# w = Especificar la ruta del diccionario 
```