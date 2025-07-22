# John the Ripper

Tags: #Hash #BruteForce #JohnTheRipper #Fcrackzip #Crackpkcs12 #PFX #OpenSSL #Hash-Identifier 

Es una herramienta de prueba de penetración que se utiliza para probar la seguridad de las passwd. Esta diseñado para descifrar passwd encriptadas en sistemas y aplicaciones.

- Soporte de múltiples formatos de passwd. Es compatible con una amplia gama de passwd, incluyendo Unix, Windows, MD5, SHA y muchos otros.
- Fuerza bruta avanzada: Es capas de realizar ataques de fuerza bruta avanzados, incluyendo ataques de diccionario, ataques basados en patrones y ataques basados en reglas. 
- Modos de operación flexibles: Admite diferentes modos de operación, incluyendo modo de ataque directo, modo de ataque de diccionario y modo de ataque hibrido. 
- Capacidad de paralelización: Es capas de utilizar múltiples núcleos de procesamiento para realizar múltiples intentos de descifrado de passwd simultáneamente, lo que mejora la velocidad y la eficiencia del ataque. 

- Casos de uso: 
	- Pruebas de penetración: Se utiliza para probar la seguridad de sistemas y aplicaciones que utilizan passwd. 
	- Auditoria de passwd: Se utiliza para auditar la fortaleza de la passwd en sistemas y aplicaciones.
	- Recuperación de passwd: Se utiliza para recuperar passwd perdidas u olvidadas. 

```bash
❯ echo -n "2b22337f218b2d82dfc3b6f77e7cb8ec" | wc -c     # Muestra el número de caracteres en una linea
```

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

## ID	Cryptographic Hash Algorithm

| \$P$ | Hash |
|---|---|
| $1 | MD5 |
| $2 o $2a | Blowfish |
| $5 | SHA-256 |
| $6 | SHA-512 |
| \$sha1$ | SHA1crypt |
| \$y$ | Yescrypt |
| \$gy$ | Gost-yescrypt |
| \$7$ | Scrypt |
## John 

```bash 
❯ john --show --format=RAW-MD5 <hash>       # Mirar la passwd crackeada en un formato especifico 
❯ john --show hash.txt                      # Otra forma de hacerlo 
```

```bash
❯ john -w:/usr/share/wordlists/rockyou.txt <Hash>     # Crackear un hash con ataque de diccionario

	# wordlist = Ruta del diccionario rockyou.txt
	# hashfile = Archivo que contiene el hash a crackear
```

## MD5 

```bash 
❯ john --format=Raw-MD5 -w:/usr/share/wordlists/rockyou.txt <Hash>    
```

## NTLM 

```bash 
❯ john --format=NT -w:/usr/share/wordlists/rockyou.txt <Hash>
```

## MSSQL 

```bash 
❯ john --format=mssql12 -w:/usr/share/wordlists/rockyou.txt <Hash> 
```

## Zip2john

```bash
❯ zip2john File.zip > <Hash>   # Devuelva el Hash para crackearlo, el resultado se ingresa en un archivo llamado 'hash'

❯ jhon -w:/usr/share/wordlists/rockyou.txt <Hash>    # Romper el hash obtenido
```

## Ssh2john

```bash 
# Esto se hace cuando tenemos un id_rsa con 'passphrase' o 'Encrypted'
❯ ssh2jhon id_rsa > <Hash>          # Pasar el id_rsa a hash con la passwd cifrada 

❯ jhon -w:/usr/share/wordlists/rockyou.txt <Hash>  # Encontrar la frase 
```

## Keepass2john

```bash 
❯ keepass2john file.kdbx > <Hash>                  # Obtener el hash del archivo con la passwd cifrada 

❯ jhon -w:/usr/share/wordlists/rockyou.txt <Hash>  # Romper el hash obtenido
```

## Pwsafe2john 

```bash 
❯ pwsafe2john file.psafe3 > hash       # Obtener el hash del archivo con la passwd cifrada

❯ jhon -w:/usr/share/wordlists/rockyou.txt <Hash>  # Romper el hash obtenido
```

## Fcrackzip

```bash 
❯ fcrackzip -p /usr/share/wordlists/rockyou.txt -b -u File.zip -D   # Crackear la password del hash del archivo comprimido en '.ZIP'

	# p = Init-passwd-string 
	# b = Bruteforce
	# D = Dicctionary 
	# u = Use-unzip 
```

## Crackpkcs12 para archivos PFX 

```bash 
❯ git clone https://github.com/crackpkcs12/crackpkcs12          # Instalar en Kali
❯ sudo apt install libssl-dev
❯ cd crackpkcs12/
❯ ./configure
❯ make
❯ sudo make install
```

``` bash 
❯ crackpkcs12 -d /usr/share/wordlists/rockyou.txt certificate.pfx    # Obtener la password del archivo 'PFX' 
	# d = Diccionario 
```

## OpenSSL para archivos PFX

```bash 
# Obtener la 'private-key.pem' del archivo 'PFX' 
❯ openssl pkcs12 -in cert.pfx -nocerts -out priv-key.pem -nodes 
	# cert.pfx = Certificado que ya se obtiene al igual que su passwd

# Obtener el 'certificado.pem' del archivo 'PFX'
❯ openssl pkcs12 -in cert.pfx -nokeys -out certificate.pem      
```