# Pop3 Post Office Protocol

Tags: #POP3 #Puerto #Comandos 

Se utiliza en clientes locales de correo para obtener los mensajes de correo electrónico almacenados en un servidor remoto, denominado servidor POP.
# Enumeración 
```bash 
❯ exiftool mail_doc.pdf    # Mirar los metadatos del archivo. A veces se encuentran correos de los usuarios 
```

## Validar credenciales (correo y contraseña) 
```bash 
# Conectar a POP3 para leer correos de un usuario válido 
❯ curl --url "pop3://IP_Server/" --user 'ControlledAccount@company.com:P@$$w0rd123!' --verbose

	# ControlledAccount = Usuario del cual se tiene un correo válido 
	# P@$$w0rd123! = Es la contraseña válida del usuario 
```

## Autenticación 
```bash
❯ nc <IP> 110                           # A veces da problemas al momento de conectarse
❯ telnet <IP> 110

	# 110 = Puerto del POP3
	# IP = Direccion de destino 
	# Telnet = Protocolo de conexion a usar 
```

## Comandos después de la autenticación 
```bash
❯ USER <Name>                    # Nombre del usuario a conectar
❯ PASS <Passwd>                  # Passwd del usuario a conectar 
❯ LIST                           # Miramos si tiene algun correo y debemos ver minimo **1** 10, por lo que no debemos de ver 0 0 ya que eso dice que no tiene correo 
❯ RETR <NUM>                     # Le pasamos el primer numero que hayamos encontrado anteriormente y listamos los mensajes que tenga en su bandeja de entrada, si encontramos un **2** quiere decir que hay dos correos, por lo que podemos poner primero **1** y despues el **2** y asi sucesivamente.
```
