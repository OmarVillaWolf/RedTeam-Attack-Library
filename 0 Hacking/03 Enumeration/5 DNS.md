# Enumeración DNS

Tags: #Hacking #Enumeration #DNS 

## Dig

```bash 
# Kali Tool 
❯ dig ns www.domain.com    # Muestra los nombres de servidores 
❯ dig @name_server www.domain.com axfr   # Transferencia de zona 
```

## NSLookup

```bash 
# Windows Tool 
❯ nslookup 
	❯ set querytype=soa
	❯ domain.com  
	❯ ls -d primary_name_server 
```

## DNSSEC Zone Walking 

```bash 
# Kali Tool 
❯ ./dnsrecon.py -h     # Muestra la ayuda de la herramienta 
❯ ./dnsrecon.py -d domain.com -z 
	# d = Especifica el dominio 
	# z = Especifica la zona DNSSEC por lo que enumerará la zona 
```

```bash 
# Más Tools 
❯ https://www.nlnetlabs.nl
❯ https://dnscurve.org 
❯ nsec3map desde https://github.com 
❯ DNSwalk desde https://github.com 
```

## Nmap 

```bash 
# Kali Tool 
❯ nmap --script=broadcast-dns-service-discovery domain.com 
❯ nmap -T4 -p 53 --script dns-brute domain.com          # Encontrar diferentes hostnames 
❯ nmap --script dns-srv-enum --script-args "dns-srv-enum.domain='domain.com'"  # Muestra el servicio 
```