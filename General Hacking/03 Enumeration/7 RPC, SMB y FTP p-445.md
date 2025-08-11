# Enumeración RPC, SMB y FTP 

Tags: #Hacking #Enumeration #RPC #SMB #FTP 

## Nstp11demo 

```bash 
# Windows Tool 
❯ Nstp11demo.exe    # Ejecutamos el programa 

Notas: 
	1. Seleccionar 'Start NetScanTools Pro Demo'
	2. En 'Manual Tools' seleccionar 'SMB Scanner' y dar click en 'Start SMB Scanner'
	3. Dar click en 'Start the DEMO'
	4. Dar click en 'Edit Target List', agregar la IP y dar click en 'Add to List'
	5. Dar click en 'Edit Share Login Credentials', agregar las credenciales y dar click en 'Add to List'
	6. Dar click en 'Get SMB Versions' para iniciar a escanear 
	7. Click derecho en la IP y seleccionar 'Shares' para ver las carpetas compartidas 


Notas: 
	1. Seleccionar 'Start NetScanTools Pro Demo'
	2. En 'Manual Tools' seleccionar 'Nix RPC Info' 
	3. Agregar la IP y dar click en 'Dump Portmap' 
```

## Nmap 

```bash 
# Kali Tool 
❯ nmap -p 21 IP   
❯ nmap -sV -T4 -A IP    # Verificar los servicios, en SMB puerto '445' puede mostrar si la firma esta activa o no 
❯ nmap -p 445 -A IP 
❯ nmap -p 21 -A IP 
```