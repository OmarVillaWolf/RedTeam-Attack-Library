# Tipos de Fuzzers 

Tags: #Wfuzz #Gobuster #Ffuf #Fuzzing #SubDomains #Directories #Dirbuster #Dirsearch #Dirb 

```python
# Modo directorio: Enumerar los directorios y archivos
❯ /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
❯ /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
❯ /usr/share/dirb/wordlists/common.txt
❯ /usr/share/metasploit-framework/data/wordlists/directory.txt

# Modo DNS: Enumerar Subdominios 
❯ /usr/share/Seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Modo vhost: Enumerar host virtuales que estén configurados en el servidor
❯ /usr/share/Seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Enumeracion a un CMS
❯ /usr/share/Seclists/Discovery/Web-Content/CMS/wordpress.fuzz.txt
```

## Gobuster

```bash
# Enumeracion de Subdominios. Gobuster trabaja muy bien con sockets y conexiones 

❯ gobuster vhost --append-domain -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --url https://❮IP❯/ -t 200 -k 

	# append-domain = Enumerar los subdominios
	# vhost = Modo enumeracion VHost Subdominios
	# w = Ruta del diccionario
	# t = Lanzar tareas en paralelo al mismo tiempo
	# k = Para certificados autofirmados para el puerto 443
```

```bash
❯ gobuster vhost --append-domain -u https://host.com/ -w /usr/share/Seclists/Discovery/DNS/subdomains-top1million-5000.txt -t 200 | grep -v "403"

	# append-domain = Enumerar los subdominios
	# vhost = Modo enumeracion VHost Subdominios
	# u = Colocamos la url
	# t = Lanzar peticiones en paralelo al mismo tiempo
	# w = Ruta del diccionario
	# grep -v = Quitamos los que salgan en codigo de estado 403
```

```bash
# Enumeración de directorios en una web 

❯ gobuster dir -u http://host.com/ -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 200 --add-slash -b 403,404

	# dir = Modo enumeracion directorios y archivos 
	# u = Colocamos la url
	# t = Lanzar peticiones en paralelo al mismo tiempo
	# w = Ruta del diccionario
	# add-slash = Ta agrega una barra al final '/' y podremos ver el codigo de estado correspondiente en lugar de 301
	# b = Para hacer Blacklist a un codigo de estado (403,404) y que no nos lo muestre
```

```bash
❯ gobuster dir -u http://host.com/ -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 200 -b 403,404 -x .php,.html,.txt,.xml -r

	# dir = Modo enumeracion directory/file
	# u = Colocamos la url
	# t = Lanzar peticiones en paralelo al mismo tiempo
	# w = Ruta del diccionario
	# b = Para hacer Blacklist a un codigo de estado (403,404) y que no nos lo muestre
	# x = Que extensiones queremos buscar (.php,.html,.txt)
	# r = Para hacer 'Follow Redirect'

❯ gobuster dir -u http://IP -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x asp,txt,html,aspx -f 

	# dir = Enumeracion de directorios y archivos
	# u = URL
	# w = Diccionario a usar
	# x = Extensiones a buscar 
	# --add-slash = Hace la misma funcion que 'f'

Nota: 
	1. Crear diccionarios propios para ser mas efectivos 
	2. En los archivos que se encuentren, mirar cada una de las rutas y observar su código fuente para ver si existe algo importante ahí 
	3. Si la aplicación web es Windows usar las siguientes extensiones: asp, aspx, html, txt...
	4. Si la aplicación web es Linux usar las siguientes extensiones: php, html, txt php5...
```

```bash
❯ gobuster dir -u https://miwifi.com/ -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 200 -s 200 -x html -b ' '

	# Despues de la 'b' colocar una cadena vacia para evitar el error
	# s = Queremos codigos de estado 200 = OK
	# dir = Modo enumeracion directory/file
	# u = Colocamos la url
	# t = Lanzar peticiones en paralelo al mismo tiempo
	# w = Ruta del diccionario
```

## Wfuzz 

```bash
# Listar subdominios 
❯ wfuzz -c --hc=404 --hh=12345 -t 200 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H “Host: FUZZ.❮IP❯” https://❮IP❯/

	# hc = HideCode 404
	# c = Formato colorido
	# hh = HideCharacters 12345
	# w = Ruta del diccionario
	# FUZZ = Donde va a insertar las palabras el diccionario
	# t = Lanzar tareas en paralelo al mismo tiempo
	# H = Para enumerar el subdominio, utilizamos esta cabecera 'Host: '
```

```bash
❯ wfuzz -c --hc=403 -t 20 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H “Host: FUZZ.tinder.com” https://tinder.com/

	# c = Permite meter colores
	# t = Lanzar tareas en paralelo al mismo tiempo
	# w = Ruta del diccionario
	# FUZZ = Donde va a insertar las palabras el diccionario
	# H = Para enumerar el subdominio, utilizamos esta cabecera 'Host: '
	# hc = HideCode 403
```

```bash
❯ wfuzz -c -L --hc=404,403 -t 200 -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt https://miwifi.com/FUZZ
❯ wfuzz -c --hc=404,403 -t 200 -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt https://miwifi.com/FUZZ/

	# L = Te aplica un Follow Redirect al codigo de estado 301, si no nos muestra nada, le quitamos el **-L** y le colocamos una barra al final 
	# hc = HideCode 403 y 404
```

```bash
❯ wfuzz -c --sl=216 --hc=404,403 -t 200 -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt https://miwifi.com/FUZZ

	# sl = ShowLine es para me muestre ese numero de lineas (l=ele) especifico
```

```bash
❯ wfuzz -c --hl=216 --hc=404,403 -t 200 -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt https://miwifi.com/FUZZ

	# hl = HideLine y sirve para ocultar ese numero de lineas
	# hw = HideWords y sirve para ocultar el numero de palabras
	# sw = ShowWords y sirve para mostrar el numero de palabras
	# hh = HideCharacter y sirve para ocultar el numero de caracteres
	# sh = ShowCharacters y sirve para mostrar el numero de caracteres
```

```bash
# Conceptos de listas 

❯ wfuzz -c --hc=404,403 -t 200 -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt https://miwifi.com/FUZZ.html

	# Para enumerar archivos html en una ruta 
```

```bash
# Crear un Payload de tipo lista 

❯ wfuzz -c --hc=404,403 -t 200 -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -z list,html-txt-php https://miwifi.com/FUZZ.FUZ2Z

	# Para enumerar diferentes archivos en una ruta 
	# z = Crear un payload de tipo lista 
	# FUZ2Z = Ahi es donde se colocaran las extensiones que hemos definido (html,php,txt)
```

```bash
# Conceptos de Range 

❯ wfuzz -c --hw=6515 -t 200 -z range,1-20000 'https://www.mi.com/shop/buy/detail?product_id=FUZZ'

	# z = Crear un payload de tipo rango
	# hw = HideWords y sirve para ocultar el numero de palabras
```

```bash
# Descubrir si tiene el Plugin de 'gwolle-gb' 

❯ wfuzz -c --hc=404 -t 200 -w wp-plugins.fuzz.txt http://❮IP❯/FUZZ

	# hc = HideCode 404
	# c = Formato colorido
	# w = Ruta del diccionario
	# FUZZ = Donde va a insertar las palabras el diccionario
	# t = Lanzar tareas en paralelo al mismo tiempo
```

## Dirbuster gráfico 

Esta herramienta sirve para enumerar directorios 
```python
❯ dirbuster &                                   # Ejecutamos el programa y nos saldra una interface como la siguiente:
```

[![Dirbuster.png](https://i.postimg.cc/XN1zQ39m/Dirbuster.png)](https://postimg.cc/LhfD8cC3)

```bash 
1. Ahí colocamos la url empezando por 'http://' y debemos de seguir la sintaxis como la de la imagen
2. Podemos activar la casilla de 'Go Faster' para que el escaneo sea mas rápido 
3. File extension: Para colocar las diferentes extensiones que queremos encontrar, a mayor extensiones, tardara mas 'pdf,docx,rar,php,zip,txt,etc...'
4. Browse: Podemos buscar el diccionario de fuerza bruta '/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt'
```

## Dirb 

```bash 
❯ man dirb                      # Mirar las opciones del comando 'dirb'
```

```python
❯ dirb http://<IP>/             # Enumerar directorios en la web por el puerto 80 
❯ dirb http://<IP>/ -f 

❯ dirb https://<IP>/            # Enumerar directorios en la web por el puerto 443
```

```python
❯ dirb http://<IP> /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -X .php # Buscar archivos 

	# X = Buscar por una extencion especifica
	# Ruta del diccionario 
	# IP = Maquina a fuzear

# Enumeracion de directorios con un diccionario especifico 
❯ dirb http://<IP> /usr/share/metasploit-framework/data/wordlists/directory.txt 
```

## Dirsearch 

```bash 
❯ dirsearch -u http://IP/ -t 30 -e txt,html,php -f -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt

	# u = URL 
	# t = Numero de peticiones 
	# e = Extensiones a buscar 'txt,html,php,jsp,asp,aspx,rar,zip'
	# f = Forzar la busqueda de extensiones 
	# w = Diccionario a utilizar 
```

## Ffuf 

```bash
❯ ffuf -h                   # Despliega el panel de ayuda
```

```bash 
❯ ffuf -w /usr/share/Seclists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeit.com" -u http://IP/ -fs 123

	# w = Ruta absoluta de la wordlist a usar 
	# H = Encabezado adicional y el server sabra que estamos enviando datos al server
	# u = Url con la ruta que se va a usar
	# fs = Palabras con el tamaño que no queremos que se muestre aunque tenga un codigo de estado de 200
```

```bash 
# Fuzzing para encontrar usuarios validos en una web 

❯ ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.186.98/customers/signup -mr "username already exists"

	# POST = Metodo a usar porque mandamos data en la peticion 
	# d = Datos a enviar (Esto dependera de los campos a llenar)
	# H = Encabezado adicional para que el server sepa que le estamos enviando datos
	# u = Url con la ruta en donde se encuentra el formulario o campos a llenar 
	# mr = Texto que buscamos validar y que hemos encontrado como usuario valido
```

```bash 
# Hacer fuerza bruta con usuarios recopilados para encontrar su password

❯ ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.186.98/customers/login -fc 200

	# W1 = Lista de usuarios recopilados 
	# W2 = Lista de diccionario a usar con las passwords 
	# X = Metodo a usar porque mandamos data a la peticion 
	# d = Datos a enviar (Esto dependera de los campos a llenar)
	# H = Encabezado adicional para que el server sepa que le estamos enviando datos
	# u = Url con la ruta en donde se encuentra el formulario o campos a llenar 
	# fc = Verificar si hay un codigo de estado HTTP distinto de '200'
```

```bash
❯ ffuf -c -t 200 -w /usr/share/Seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u https://miwifi.com/FUZZ/ -v --mc=200 

	# Si queremos hacer el Follow Redirect para el codigo 301 lo mejor es colocarle una '/' al final de FUZZ
	# c = Permite meter colores
	# u = Colocamos la url
	# w = Ruta del diccionario
	# t = Lanzar peticiones en paralelo al mismo tiempo
	# v = Verbose, te dice a donde te redirige el codigo 301 y los demas
	# mc = MatchCode y sirve para filtrar por el codigo de estado 200
	# FUZZ = Ahi se van a sustituir las palabras del diccionario
```

```bash
❯ ffuf -u http://❮IP❯/FUZZ -w /usr/share/seclists/Discovery/Web-Content/big.txt 

	# u = url 
	# w = ruta del diccionario 
	# FUZZ = Ahi se van a sustituir las palabras del diccionario
	# /usr/share/wordlists/dirb/big.txt 
```

```bash
# Para encontrar diferentes archivos 
❯ ffuf -u http://❮IP❯/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.tx

# Buscamos por extenciones especificas
❯ ffuf -u http://❮IP❯/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e .php,.txt,.html

# Para fuzzear los directorios en la pagina web
❯ ffuf -u http://❮IP❯/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt
```


