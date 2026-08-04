# Pivoting con SSH

Tags: #Pivoting #SSH #DinamicForwarding 

## Dinamic Port Forwarding 

```bash 
❯ ssh -D 1080 user@IP           # Conectarse por SSH creando un túnel dinámico en la máquina local. Por lo que cualquier conexión de red al puerto 1080 en tu computadora se redirigirá a través de la sesión SSH al servidor remoto

❯ netstat -ant | grep 1080      # Verificar la creación del túnel de manera local (modo: LISTENING)
```

```bash 
❯ nano /etc/proxychains.conf    # Modificar el archivo 'proxychains' y agregar lo siguiente:
	socks4 127.0.0.1 1080       # Comentar (# proxy_dns) y modificar el proxy en 'ProxyList'
```

## Comandos con Proxychains

```bash 
Siempre se debe colocar 'proxychains' antes de cada comando por lo que hará el comando pase por el tunel creado por chisel

- NMAP
❯ seq 1 65535 | xargs -P 500 -I {} proxychains nmap -sT -Pn -p{} -open -T5 -v -n ❮Target IP❯ 2>&1 | grep "tcp open"

	# P = Tareas en paralelo

❯ proxychains nmap --top-ports 500 --open -T5 -v -n <IP> -sT -Pn -oG allports 2>&1 | grep -vE "timeout|OK"
❯ proxychains nmap -sT -Pn -sCV -p22,.. <IP> -oN Targeted | grep -vE "timeout|OK"


- WHATWEB
❯ proxychains whatweb IP          # Escaneo hacia la maquina víctima 


- NETCAT
❯ proxychains nc -nv IP 445       # Ejecutar netcat en el puerto 445
	# n = No aplicar DNS
	# v = Verbose 


- REMOTE DESKTOP
❯ proxychains rdesktop IP          # Conectarse por RDP utilizando un usuario y passwd validos. Este comando abrirá una ventana 'Windows' para hacer el login con las credenciales 


- VNCVIEWER
❯ proxychains vncviewer -passwd secret 127.0.0.1:5901   # Conectarse por vncviewer

	# secret = Archivo que contiene la passwd para la conexión
```

## X11 Forwarding 

```bash 
❯ ssh -X user@IP_Remoto
```