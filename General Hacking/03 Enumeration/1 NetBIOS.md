# Enumeración del NetBIOS 

Tags: #Hacking #Enumeration #NetBIOS 

```bash 
# Windows Tool 
❯ nbtstat -a IP    # Despliega el nombre de la tabla del NetBIOS del computador remoto 
❯ nbtstat -c       # Lista el contenido de cache de NetBIOS, nombre y sus direciones IP 

❯ net use          # Despliega info del target, status, folder/disco e info de red 
```

## NetBIOS Enumerator 

```bash 
# Windows Tool 
❯ NetBIOS Enumerator    # Ejecutamos el programa 

Nota: 
	1. Agregar el rango de IPs y darle en 'Start'
	2. Desplegar el '+' para ver mas info de cada IP
```

## Nmap 'NSE Script'

```bash 
# Kali Tool 
❯ nmap -sV -v --script nbstat IP 
	# sV = Detecta la versión 

❯ nmap -sU -p 137 --script nbstat IP 
```

```bash 
# Más Tools 
❯ http://www.magnetosoft.com
❯ https://www.advanced-ip-scanner.com
❯ https://www.systemtools.com
❯ https://www.nsauditor.com 
```