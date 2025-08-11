# Enumeración SNMP 

Tags: #Hacking #Enumeration #SNMP 

## Nmap 

```bash
# Kali Tool 
❯ nmap -sU -p 161 IP   
❯ nmap -sU -p 161 --script snmp-sysdescr IP 
❯ nmap -sU -p 161 --script snmp-processes IP 
❯ nmap -sU -p 161 --script snmp-win32-software IP
❯ nmap -sU -p 161 --script snmp-interfaces IP 
```

## SNMP Check 

```bash 
# Kali Tool 
❯ snmp-check IP    # Enumera la maquina target, listando info sensible como información del systema y cuentas de usuario 
```

## SoftPerfect Network Scanner 

```bash 
# Windows Tool 
❯ Network Scanner     # Ejecutamos el programa 

Nota:
	1. Ir a 'options' y seleccionar 'Remote SNMP' 
	2. Seleccionar todos los servicios con 'Mark All/None'
	3. Colocar le rango de las IP y dar click en 'Start Scanning'
	4. Se pueden ver las propiedades de cada IP como los recursos compartidos, Direcciones IP, MAC Address, Tiempo de respuesta, Nombre del host, tiempo arriba y la descripción del systema 
	5. Al una IP especifica se le puede dar click derecho y seleccionar 'Open Device' para conectarse a ella  
```

```bash 
# Más Tools 
❯ https://www.solarwinds.com 
❯ https://www.manageengine.com
❯ https://www.paessler.com
```

## Snmp Walk 

```bash 
# Kali Tool 
❯ snmpwalk -v1 -c public IP   
	# v = Especifica el numero de versión  (1, 2 o 3)
	# c = Nombre del grupo 

❯ snmpwalk -v2c -c public IP 
```