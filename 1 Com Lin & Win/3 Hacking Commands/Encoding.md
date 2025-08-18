# Encoding 

Tags: #Encoding 

Encoding en la web se refiere al proceso de representar data e información en una estructura de formato que le permite ser transmitida, almacenada e interpretada por computadoras y humanos. Encoding es de vital importancia para transferir y representar información en el Internet. Asegura que la data como texto, imágenes, archivos y multimedia se puedan comunicar efectivamente y desplegada a través de las tecnologías web ya que convierte la data desde su formato original a un formato que sea adecuado para su transmisión digital y almacenamiento mientras preserva su integridad.  

Ejemplos de **Character Sets:** ASCII, Unicode, Latin-1, etc.

```bash 
❯ https://www.ascii-code.com/               # ASCII 
❯ https://www.utf8-chartable.de/            # Unicode UFT8
❯ https://www.charset.org/                  # CharSet

❯ https://gchq.github.io/CyberChef/         # Nos ayuda a encodear

❯ https://www.w3schools.com/                # Podemos hacer HTML enconde
```

## HTML Encoding 

```php  

# La web interpretara el PHP y agregara la demas a la pagina web.

❯ nvim html_encoded.php          # Creamos el archivo 

 <!DOCTYPE html>
 <html>
	 <head>
		 <title>HTML Encoding</title>
	 </head>
	 <body>
		 <h1>This is a simple html</h1>
		 <?php
			 $userInput = $_GET['input'];
			 $sanitizedInput = htmlspecialchars($userInput,ENT_QUOTES,'UTF-8'); # Esta linea es para no procesar caracteres del lenguaje HTML
			 echo "<p>User input from the URL: $userInput</p>";   # Agregar $sanitizedInput en lugar de $userInput para finalizar la sanitizacion 
		?> 
		<p>Thank you for visiting!</p>
	 </body>
 </html>


# Ahora en la URL podemos ingresar los 'input' y ver como procesa lenguaje HTML

❯ http://localhost/html_encoded.php?input=<strong>Omar</strong>                     # Inyeccion HTML
❯ http://localhost/html_encoded.php?input=<script>alert("Omar")</script>            # Etiqueta script 
```


## URL Encoding 

```bash 
# El UrlEncode(String) método se puede usar para codificar toda la dirección URL, incluidos los valores de cadena de consulta. Si se pasan caracteres como espacios en blanco y puntuación en una secuencia HTTP sin codificación, podrían interpretarse erróneamente al final receptor.

<script>alert("1")</script>       =       %3Cscript%3Ealert%281%29%3C%2Fscript%3E 

Encodeo
	# %20 = Espacio en la URL  
	# %3C = <
	# %3E = >
	# %28 = (
	# %29 = )
	# %2F = /
```


## Base64 Encoding 

```bash 
# Es un método utilizado para encodear datos binarios (imagenes, archivos de audio, y otros non-text data) dentro de formato basado en texto. Este tipo de enconding es para representar datos binarios en contextos donde solo la data textual es soportada, por ejemplo dentro de un mensaje de email o en las URLs. 

❯ base64 image1.jpg                            # Encodear en Base64 en Kali
❯ echo 'Code-Base64' | base64 -d               # Decodear 
```