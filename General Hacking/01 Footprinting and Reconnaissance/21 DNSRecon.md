# DNSRecon 

Tags: #Hacking #Footprinting #DNS 

```bash 
# Website 
❯ https://www.yougetsignal.com     # Buscar los diferentes dominios asociados a la misma dirección IP 

Nota: 
	1. Ir a la opción 'Reverse IP Domain Check'
	2. Agregar el dominio 'www.domain.com'


# Kali tool
❯ python3 dnsrecon.py -r 10.10.10.0-10.10.10.255    # Muestra los hosts asociados a cada dirección IP

	# r = Rango de direcciones IP
```