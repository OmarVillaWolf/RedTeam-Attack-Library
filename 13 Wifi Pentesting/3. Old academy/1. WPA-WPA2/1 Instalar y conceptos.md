# Conceptos 

Tags: #Wifi #Tool #Airodump-ng #Airmon-ng 

```bash 
Curso de preparación para el OSWP en el siguiente Github: 'https://s4vitar.github.io/oswp-preparacion/'
```

```bash 
El dispositivo Wifi que se usa es el 'ALFA - AWUS036ACM' 

# Instalación en Kali 
❯ git clone https://github.com/aircrack-ng/rtl8812au 
❯ cd rtl8812au
❯ make 
❯ sudo make install 
```

```bash 
# Tools 
1. Suite Aircrack
2. Wireshark
3. Wpa_suplicant
4. Hostapd-mana
5. Openssl
6. Asleap
7. Dhclient
```

* [Aircrack-ng - Comandos ](https://yisux.wordpress.com/2009/03/11/aircrack-ng-comandos-basicos-para-ataques-con-clientes-asociados/)

## Airmon-ng

```bash 
Este script puede usarse para activar el modo monitor de las tarjetas wireless. Tambien puede usarse para parar las interfaces y salir del modo monitor. Si escribimos el comando airmon-ng sin parámetros veremos el estado de nuestras tarjetas
```

## Airodump-ng

```bash 
Al momento de iniciar la captura tenemos dos partes. La superior indica la seccion de los AP (Puntos de Acceso), mientras que la inferior indica la seccion de los clientes concectados o no a AP. 

# Parte Superior 
1. BSSID - Direccion MAC del AP
2. PWR - Nivel de señal. Si aumenta la señal, mas cerca estas del AP. (Si PWR = -1)  No admite niveles de señal
3. Beacons - Numeros de paquetes de anuncios enviados por el AP 
4. Data - Numero de paquetes capturados (Incluyendo los paquetes de transmisión de datos) 
5. gato/s - Numero de paquetes de datos por segundo medidos durante los ultimos 10 seg
6. CH - Canal en donde se encuentra el AP 
7. MB - Velocidad máxima permitida por el AP
8. ENC - Es el cifrado detectado (WEP, OPN (Open), WPA, WPA2)
9. CIPHER - Cifrado empleado (CCMP, TKIP, WEP40, WEP104)
10. AUTH - Protocolo de autenticación (PSK, MGT, SKA, OPN)
11. ESSID - Nombre de la red inalambrica (Se recomienda filtrar por esta parte), cuando en el ESSID aparece un '<length: 14>' quiere decir que el nombre de la red esta oculta y lo podemos obtener por 'Fuerza Bruta'  

# Parte inferior 
1. BSSID - Dirección MAC de los puntos de acceso del AP
2. Station - MAC de los clientes y son dispositivos que estan conectados al BSSID (Clientes conectados a la red inalambrica)
3. PWR - Nivel de señal. Si aumenta la señal, mas cerca estas del AP
4. Rate - Tasa de recepción y la tasa de retransmisión 
5. Lost - Paquetes perdidos en los ultimos 10 seg
6. Frames - Paquetes enviados por el cliente al AP
7. Notes - Información adicional 
8. Probes - Lista los nombres de las redes en donde un cliente ha estado conectado (presente y pasado), 

# EvilTwin
Este tipo de ataque se efecutua cuando te desconectas del AP, despues al querer volver a conectarte, tu dispositivo busca el 'Probes' o nombre de la red donde estabas autenticado, por lo que es ahí cuando podemos hacer un ataque 'Evil-Twin', que nos permite crear un AP con el nombre de la red que conocemos a base del 'Probes' y hacer que el dispositivo se conecte a nuesto AP malicioso. 

# HandShake
Cuando te alejas de una red y te desconectas, al momento de acercarte al AP, tu dispositivo primero busca el nombre del AP al que estaba conectado y despues hace una validación ('Handshake') proporcionando las credenciales para volverse a conectar automaticamente, es ahí donde aprovechas ese momento para capturar los paquetes que se transmiten con la passwd, despues tocaria descifrarla con ataque de fuerza bruta. Si la passwd es debil y se encuentra en el diccionario la podremos obtener.
```