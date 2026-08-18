# Pop3 Post Office Protocol

Tags: #POP3 #Puerto #Comandos 

Se utiliza en clientes locales de correo para obtener los mensajes de correo electrónico almacenados en un servidor remoto, denominado servidor POP.

```bash
❯ nc <IP> 110                           # Aveces nos da problemas al momento de conectarnos
❯ telnet <IP> 110

	# 110 = Puerto del POP3
	# IP = Direccion de destino 
	# Telnet = Protocolo de conexion a usar 
```

```bash
# Comandos cuando nos conectamos
❯ USER <Name>                    # Nombre del usuario a conectar
❯ PASS <Passwd>                  # Passwd del usuario a conectar 
❯ LIST                           # Miramos si tiene algun correo y debemos ver minimo **1** 10, por lo que no debemos de ver 0 0 ya que eso dice que no tiene correo 
❯ RETR <NUM>                     # Le pasamos el primer numero que hayamos encontrado anteriormente y listamos los mensajes que tenga en su bandeja de entrada, si encontramos un **2** quiere decir que hay dos correos, por lo que podemos poner primero **1** y despues el **2** y asi sucesivamente.
```
