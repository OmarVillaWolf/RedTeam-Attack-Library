# Hashcat

Tags: #HashCat #Hash-Identifier #DictionaryAttack #BruteForce 

## Identificar Hash

* Pagina para identificar los hashes: [Identificar_Hashes](https://hashes.com/en/tools/hash_identifier) 
* Pagina para crackear los hashes: [Crackear_Hashes](https://crackstation.net/)

```bash
❯ hashid <2b22337f218b2d82dfc3b6f77e7cb8ec>   # Identificar el tipo de hash 

❯ hash-identifier                             # Identificar el tipo de hash

Notas:
	1. MD5 = Tiene 32 caracteres
	2. Hash NTLM
	administrator:500:LM:NT:::     # Solo se necesita la parte de NT para crackear la password
	administrador:500:42f29043y123fa9c74f23606c6g522b0:71759a1bb2web4da43e676d6b7190711:::
```

## Ataque de Diccionario 

* [Hashcat-ExampleHashes](https://hashcat.net/wiki/doku.php?id=example_hashes)

```bash
❯ hashcat --help     # Nos muestra el panel de ayuda de la tool y algunos ejemplos

❯ hashcat --example-hashes     # Muestra todos los tipos de hashes 
❯ hashcat --example-hashes | grep <hash> -B 10 # Filtrar por el hash y leer las 10 lineas de arriba

# Podemos crakear de uno en uno
❯ hashcat -m 0 hashes.txt /usr/share/wordlists/rockyou.txt -d 1 -D 2
❯ hashcat -m 5600 hashes.txt rockyou.txt -d 1 -D 2
❯ hashcat -m 400 -a 0 hash.txt rockyou.txt -d 1 -D 2

	# m = Tipo de hash a evaluar (0 = MD5)
	# a = Tipo de ataque  
	# 400 = Inicia con $P$ y es phpass, WordPress (MD5), Joomla (MD5)
	# hashes.txt = Archivo que contiene el hash
	# D = Tipo de dispositivo (2 = GPU)
	# d = ID de la GPU a usar en 'OpenCL' (1 = GPU Nvidia con memoria de 8064 MB). Varia en cada maquina 
	# w = Perfil de Workload (1=Low, 2=Default, 3=High, 4=Nightmare).
```

```bash
❯ hashcat.exe --stdout -r rules/best64.rule hash.txt > passwords  # Crear un diccionario con las variantes de la password almacenada en el archivo hash.txt 
```

## NTLM 

```bash 
❯ hashcat -m 5600 -a 0 hashes.txt rockyou.txt --force -O          
	# 5500 = NetNTLMv1 / NetNTLMv1+ESS
	# 5600 = NetNTLMv2 
	# O = Aumentar la velocidad del crackeo
```

## ASREP TGT

```bash 
❯ hashcat -m 18200 -a 0 --force --rules /usr/share/hashcat/rules/InsidePro-PasswordsPro.rule asrep.hash rockyou.txt 
	# --show = Muestra las passwd que ya han sido crackeadas 'historial'
```

## Kerberos TGS-REP 

```bash 
❯ hashcat -m 13100 -a 0 --force --rules /usr/share/hashcat/rules/InsidePro-PasswordsPro.rule kerb.hash rockyou.txt 
	# --show = Muestra las passwd que ya han sido crackeadas 'historial'
```

## MSSQL

```bash 
❯ hashcat -m 1731 -a 0 hash.txt rockyou.txt --force 
	# --show = Muestra las passwd que ya han sido crackeadas 'historial'
```

## Fuerza Bruta

```bash 
# Se debe de usar en Windows con la GPU 
❯ hashcat.exe -m 22000 Hash_File -a 3 "?d?d?d?d?d?d?d?d?d?d" -d 1 -D 2 -w 3

	# m = Tipo de hash a evaluar (22000 = WPA-PBKDF2-PMKID+EAPOL )
	# a = Tipo de ataque (3 = Fuerza bruta)
	# "d?" = Cantidad de digitos a probar. A veces funciona sin comillas 
	# D = Tipo de dispositivo (2 = GPU)
	# d = ID de la GPU a usar en 'OpenCL' (1 = GPU Nvidia con memoria de 8064 MB). Varia en cada maquina 
	# w = Perfil de Workload (1=Low, 2=Default, 3=High, 4=Nightmare). 
```