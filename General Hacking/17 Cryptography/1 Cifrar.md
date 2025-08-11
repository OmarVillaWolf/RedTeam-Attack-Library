# Cifrar información 

Tags: #Hacking #Criptografia #Ñ 

## Obtener Hash

```bash 
# Kali Tool 
❯ sha384sum file.elf      # Calcular el SHA-384
```

## HashMyFiles

```bash 
# Windows Tool para calcular y comparar hashes 
❯ hashmyfiles.exe       # Ejecutar el programa 

Notas:
	1. Seleccionar el folder para calcular los hashes 'MD5, SHA1' de todos los archivos
	2. En 'Options > HashTypes' se puede agregar o quitar los diferentes hashes  
```

## CryptoForge

```bash 
# Windows Tool para cifrar o descifrar archivos 
❯ cryptoforge.exe       # Ejecutar el programa y se colocará como opción al momento de dar clic derecho en un archivo 

Notas:
	1. Dar click derecho sobre un archivo y seleccionar 'Encrypt' 
	2. Agregar una password y cifrar el documento 


❯ cryptoforgeText.exe   # Sirve para escribir un texto y despues cifrarlo 

Notas:
	1. Escribir un texto y seleccionar 'Encrypt' 
	2. Agregar una password y cifrar el documento 
	3. Se puede guardar como un archivo 
```

## BCTextEncoder

```bash 
# Windows Tool para cifrar y descifrar archivos de texto 'Encode, Decode'
❯ BCTextEncoder.exe      # Ejecutar el programa 

Notas:
	1. Escribir un texto en la parte de arriba y seleccionar 'Encode' 
	2. Agregar una password y cifrar el documento

Notas2:
	1. Se generará un archivo con un banner con la siguiente leyenda "BEGIN ENCODE MESSAGE"
	2. Además de, mostrar la versión, mostrar el enconder utilizado "BCTextEncoder" y el texto cifrado 

Notas3: 
	1. Para descifrar el mensaje, se debe de copiar todo el texto incluyendo el banner del archivo en la herramienta en la parte de abajo
	2. Dar click en 'Decode' y agregar la contraseña
```

## Cryptool 

```bash 
# Windows Tool para cifrar o descifrar data '.hex' manipulando la longitud de llave haciendo un cripto analisis   

❯ CrypTool.exe        # Ejecutar el programa 

Notas:
	1. Abrir el archivo con extensión hexadecimal 
	2. Dar click en 'Analysis > Symmetric Encryption (Modern) > RC4'
	3. Modificar la longitude de llave '8, 16 bits' y dar click en 'Start'
	4. En el resultados en la parte de descripción se observará el texto  

Notas2:
	1. Abrir el archivo con extensión hexadecimal 
	2. Dar click en 'Encrypt/Decrypt > Symmetric (Modern) > Further Algorithms > Twofish'
	3. Coloca la longitud de llave '128, 192, 256 bits', además de, el numero de cifrado como el 06 y se coloca en todos los ceros que aparecen
	4. Al finalizar dar click en 'Decrypt' para ver el mensaje oculto 
```

## HashCalc

```bash 
# Windows Tool donde se carga un archivo y devuelve los diferentes hashes
❯ hashcalc.exe        # Sirve para comparar el hash en un archivo 

Notas:
	1. Seleccionar archivo y subir el documento 
	2. Dar click en calcular 
	3. Solo muestra los algoritmos seleccionados 
```

## MD5 Calculator 

```bash 
# Windows Tool
❯ md5_calculator.exe   # Ejecutar el programa 

Notas:
	1. Seleccionar el archivo y subir el documento 
	2. Dar click en 'calculate' y muestra el hash 'MD5'
	3. Dar click en 'Copy' para copiar el hash que se ha mostrado y pegarlo en 'Verify MD5 Value'
	4. Despues, remover el archivo y modificarlo 
	5. Volver a subir el archivo y compararlo 
```

## AES tool 

```bash 
# Windows Tool
❯ AES-tool2.7.exe    # Ejecutar el programa para cifrar o descifrar un archivo '.aes'

Notas:
	1. Abrir el archivo a descifrar 
	2. Colocar la password si es que el archivo la solicita 
	3. Dar click en 'Decrypt' y colocar la ruta en donde se guardara el archivo 
```