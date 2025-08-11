# Intrusion Detection 

Tags: #Hacking #IntrusionDetection 

## Snort

```bash 
# Windows Tool
❯ Snort2.9.15.exe        # Ejecutar el programa 

Nota:
	1. Por defecto se instala en la siguiente ruta: 'C:\Snort'
	2. Copiar de la carpeta 'Snort\etc\snort.conf' el archivo a la ruta 'C:\Snort\etc' reemplezandolo 
	3. Copiar la carpeta 'Snort\so_rules' a la ruta 'C:\Snort'
	4. Copiar la carpeta 'Snort\preproc_rules' a la ruta 'C:\Snort' reemplazandolo
	5. Copiar la carpeta 'Snort\rules' a la ruta 'C:\Snort' reemplazandolo
	6. Parar con 'Ctrl + c' antes de modificar el archivo 

Nota2:
	1. Ejecutar un 'cmd' e ir la ruta 'C:\Snort\bin'
	❯ snort          # Inicializar el programa. Despues de completar presionar 'Ctrl + c'
	❯ snort -W       # Muestra info de la máquina física 'MAC', IP, nombre y descripción. Si esta en ceros es que escuchará en todos 
	❯ snort -dev -i 1     # Habilitar el driver con interface 1 e inicia el procesamiento de paquetes 

Nota3:
	1. Ejecutar otro 'cmd' y hacer ping a 'www.domain.com' para generar logs en Snort

Nota4:
	1. Configurar el archivo 'C:Snort\etc\snort.conf' abriendolo con notepad
	2. Ir a 'STEP #1: Set the network variables' y reemplazar 'any' en 'HOME_NET' por la dirección IP del servidor 
	3. Reemplazar '$HOME_NET' de 'DNS_SERVER' y colocar '8.8.8.8'
	4. Reemplazar la ruta '../rule' de 'RULE_PATH' y colocar 'C:\Snort\rules'
	5. Reemplazar la ruta '../so_rules' de 'SO_RULE_PATH' y colocar 'C:\Snort\so_rules'
	6. Reemplazar la ruta '../preproc_rules' de 'PREPROC_RULE_PATH' y colocar 'C:\Snort\preproc_rules'
	7. En 'WHITE_LIST_PATH y CLACK_LIST_PATH' colocar 'C:\Snort\rules' y guardar 
	8. Ahora en la carpeta de 'C:\Snort\rules' crear los archivos: 'white_list.rules y black_list.rules', si es necesario cambiar la extensión 'txt a rules' 
	9. En el archivo 'C:Snort\etc\snort.conf' ir a 'STEP #4: Condigure dynamic loaded libraries'
	10. Reemplazar por la ruta 'C:\Snort\lib\snort_dynamicpreprocessor' en 'dinamicpreprocessor directory'
	11. Reemplazar por la ruta 'C:\Snort\lib\snort_dynamicengine\sf_engine.dll' en 'dinamicengine'
	12. Dar un espacio  y agregar gato a '# dynamicdetection directory ', quedará de esta manera 
	13. En el 'STEP #5: Configure preprocessors'
	14. Comentar con '#', darle un espacio y quedará de esta manera en '# dynamicdetection directory' 
	15. Comentar con '#', darle un espacio y quedará de esta manera en '# preprocessor normalize_ip4, # preprocessor normalize_tcp: block ... y # preprocessor normalize_ip6' ademas de dar un espacio a los restantes que tienen un comentario 
	16. Borrar 'lzma' en 'decompress_svf (defalte lzma)'
	17. En el 'STEP 6: Configure output plugins' 
	18. Colocar la ruta 'C:\Snort\etc\classification.config y C:\Snort\etc\reference.config' despues de 'Include' en '# metadata reference'
	19. Agregar en la linea de abajo 'output alert_fast: alerts.ids:'
	20. Con 'Find = Ctrl + f' pestaña 'Replace' en 'Notepad' reemplazar la palabra 'Ipvar' por la palabra 'var' en todo el texto 

Nota5:
	1. Configurar el archivo 'C:\Snort\rules\icmp-info.rules' abriendolo con notepad
	2. Agregar al final la siguiente regla 'alert icmp $EXTERNAL_NET any -> $HOME_NET IP(msg:"ICMP-INFO PING"; icode:0; itype:8; reference:arachnids,135; reference:cve,1999-0265; classtype:bad-unknown; sid:472; rev:7;)' y guardar 
		# IP = Dirección IPv4 local, esto con el fin de cuando se haga ping a nuestra máquina se muestren las alertas. Los logs se guardan en 'C:\Snort\log'

Nota6: 
	1. Ejecutar un 'cmd' e ir la ruta 'C:\Snort\bin'
	❯ snort -iX -A console -c C:\Snort\etc\nosrt.conf -l C:\Snort\log -K ascii 
		# Reemplazar 'X' por el numero de dispositivo '❯ snort -W' elcual puede ser '1'
		# Debe de salir 'Commencing packet processing (pid=xxxx)'

Nota7: 
	1. Hacer ping desde la maquina de atacante Windows 
```

## ZoneAlarm FREE FIREWALL 

```bash 
# Windows Tool para detectar tráfico malicioso 
❯ zafwSetupWeb158.189.19019.exe        # Ejecutar el programa 

Nota: 
	1. Aceptar los teminos de licencia y dar click en 'Custom Install'
	2. Seleccionar 'Sey Application Control to AUTO-LEARN mode'
	3. Dar click en 'Skip' para que no se agregue la extensión a Google Chrome
	4. Dar click en 'Finish'

Nota2: 
	1. Dar click en 'Firewall' e ir a 'View Zones'
	2. Dar click en 'ADD > Host/Site'  
	3. En Zone colocar 'Blocked', Hostname: 'www.domain.com', agregar una descripción y dar click en 'Lookup'
```

## HoneyBOT 

```bash 
# Windows Tool para detectar tráfico malicioso 
❯ HoneyBOT_018.exe        # Ejecutar el programa 

Nota:
	1. Ejecutar el programa como 'Administrator' 
	2. Dar click en 'Options' en la pestña de 'Exports', seleccionar 'Export Logs to CSV'
	3. En la pestaña de 'Updates' desmarcar la opción de 'Check for Updates' y dar click en 'Apply'
	4. Si hay conexiones se moestrarán en los logs 
```