# Enumeration Tools

Tags: #Hacking #Enumeration 

## Global Network Inventory

```bash 
# Windows Tool 
❯ global network inventory       # Ejecutar el programa 

Nota:
	1. Seleccionar 'Single Address Scan' o 'IP Range Scan'
	2. Agregar la dirección IP 
	3. Seleccionar 'Connect as', agregar las credenciales y finalizar para comenzar a escanear 
	4. En la pestaña de 'Scan Summary' se puede ver la info del ordenador posicionandose en él 
	5. Se puede explorar cada pestaña para observar los resultados obtenidos 
```

## Advanced IP Scanner 

```bash 
# Windows Tool 
❯ Advanced IP Scanner       # Ejecutamos el programa 

Nota: 
	1. Colocar un rango o una IP 
	2. Dar click en 'Scan' 
	3. Se puede ver la info de cada host expandiendolo 
	4. Dar click derecho y seleccionar 'Tools' y seleccionar una opción
```

## Enum4linux

```bash 
# Kali Tool 
❯ enum4linux -h        # Mirar el panel de ayuda 
❯ enum4linux -u user -p password -n IP      # Enumeración y despliega info del NetBIOS 
❯ enum4linux -u user -p password -U IP      # Muestra una lista de usuarios
❯ enum4linux -u user -p password -o IP      # Muestra info del OS
❯ enum4linux -u user -p password -P IP      # Muestra las politicas para passwords
❯ enum4linux -u user -p password -G IP      # Muestra los grupos y miembros 
❯ enum4linux -u user -p password -S IP      # Muestra los recursos compartidos 
```