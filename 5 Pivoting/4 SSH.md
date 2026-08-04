# Pivoting

Tags: #Pivoting #SSH 

El **pivoting** (también conocido como “hopping”) es una técnica utilizada en pruebas de penetración y en el análisis de redes que implica el uso de una máquina comprometida para atacar otras máquinas o redes en el mismo entorno.

Por ejemplo, si un atacante ha comprometido una máquina en una red corporativa, puede utilizar técnicas de pivoting para utilizar esa máquina como punto de salto para atacar otras máquinas en la misma red que de otra manera no serían accesibles. Esto se logra a través de la creación de túneles de comunicación desde la máquina comprometida a otras máquinas en la red.

El pivoting puede ser utilizado para superar restricciones de seguridad que de otra manera impedirían a un atacante acceder a determinadas máquinas o redes. Por ejemplo, si una red corporativa utiliza segmentación de red para separar diferentes partes de la red, el pivoting puede ser utilizado para superar esta restricción y permitir que un atacante salte de una red a otra.

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