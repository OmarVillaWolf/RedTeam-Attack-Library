# Esteganografía 

Tags: #Hacking #Steganography #Snow #OpenStego #Ñ 

# Espacios en blanco 
## Snow

```bash 
# Windows Tool para esconder y extraer data escondida en un archivo de texto y se ejecuta desde el CMD

❯ Snow.exe -C File.txt                   # Extraer datos 
❯ Snow.exe -C -p "password" File.txt     # Extraer datos con la password de archivo 

❯ Snow.exe -C -m "Hola" -p "password" File.txt Hidden.txt    # Forma de ocultar un mensaje 
	# m = Mensaje a escribir 
	# p = Contraseña que tendra el archivo 
	# File.txt = Archivo en donde guardaremos el mensaje oculto 
	# Hidden.txt = El archivo resultante 

Nota:
	1. Si hay un texto oculto con un mensaje, se puede observar al seleccionar el contenido del archivo de texto, ya que habrá espacios en blanco seleccionables. 
```

# Imágenes 
## OpenStego

```bash 
# Web Tool 
❯ https://georgeom.net/StegOnline/upload


# Windows Tool para esconder y extraer data escondida en un archivo de imagen 
❯ OpenStego.exe          # Ejecutar el programa 

Notas:
	1. Seleccionar 'Extract Data' en 'Data Hiding' y cargar el archivo en 'Input'
	2. Dar click en 'Output' y colocar la ruta del directorio en donde se guardará el archivo de texto con la info extraida de la imagen 
	3. Si la imagen contiene una contraseña, colocarla para poder extraer la data adecuadamente 
	4. Dar click en 'Extract data'
```

## Convert TCP 

```bash 
❯ wget https://github.com/zaheercena/Covert-TCP-IP-Protocol/blob/master/covert_tcp.c  # Descargar

❯ cc -o convert_tcp convert_tcp.c     # Compilar la herramienta 
```

```bash 
# Kali Tool para esconder data en cabeceras TCP/IP a traves de la red, enviando datos en los espacios sobrantes presentes en la cabecera de 1 byte a la vez (La herramienta lo hará automáticamente)

❯ ./convert_tcp -source IP -dest IP -source_port 9999 -dest_port 8888 -file file.txt   # Para enviar  
	# source = IP del cliente o de la propia maquina 
	# dest = IP del destino 
	
❯ ./convert_tcp -source IP -source_port 8888 -server -file receive.txt   # Para recibir
	# source = IP de quien lo envia 
	# source_port = Puerto en donde se recibirá el archivo 
```

## Entropía

```bash 
# Kali Tool 
❯ apt install ent  

❯ ent file.elf              # Calcular la entropía
```

