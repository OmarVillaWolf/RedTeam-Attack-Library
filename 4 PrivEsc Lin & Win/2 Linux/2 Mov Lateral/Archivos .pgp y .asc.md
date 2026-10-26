# Archivos .pgp y .asc

Tags: #Credenciales 

Los archivos `.pgp` y `.asc` suelen estar relacionados con OpenPGP, un estándar criptográfico que permite cifrar información, firmar archivos y verificar identidades. Pero hay una diferencia importante: la extensión no siempre te dice exactamente qué contiene el archivo.

`.pgp` — Archivo OpenPGP
Generalmente contiene datos cifrados o información protegida con OpenPGP. Suele estar en formato binario, aunque también puede contener otros tipos de paquetes OpenPGP.
Ejemplo: `backup.pgp`

`.asc` — Archivo ASCII-armored
Normalmente contiene datos OpenPGP representados como texto ASCII. Puede ser una clave pública, una clave privada, un mensaje cifrado o una firma digital.
Ejemplo: `publickey.asc`

## Forma directa 
```bash 
Paso 1:
# Importar la clave privada
❯ gpg --import clave_privada.asc

Paso 2:
# Descifrar el archivo
❯ gpg --decrypt archivo.pgp
```

## Crackear la passphrase 
```bash 
# Extraer el hash de la clave privada
❯ gpg2john file.asc > hash.txt

❯ john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```