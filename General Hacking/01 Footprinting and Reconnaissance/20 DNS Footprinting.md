# DNS Footprinting 

Tags: #Hacking #Footprinting #DNS 

```bash 
# Windows tool 
❯ nslookup 
	❯ set type=a          # Tipo de registro a consultar 'a = Address'
	❯ www.domain.com      # Agregar el dominio a consultar y devolverá la IP con respuesta no autoritativa
	
	❯ set type=cname      # Mirar el 'primary name server' 'cname = canonical name' respuesta autoritativa 
	❯ www.domain.com      # Agregar el dominio a consultar 

	❯ set type=a          # Tipo de registro a consultar 'a = Address'
	❯ ns1.bluehost.com    # Agregar el 'primary name server' y devolverá la IP con respuesta no autoritativa


# Website 
❯ https://www.kloth.net/services/nslookup.php 

Nota:
	1. Agregar el domino a analizar
	2. Colocar el tipo de consulta 'Query', puede ser IPv4, IPv6, texto, etc...


# Más tools
❯ https://dnsdumpster.com
❯ https://network-tools.com 
```