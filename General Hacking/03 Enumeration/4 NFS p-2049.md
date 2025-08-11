# Enumeración NFS (Network File System)

Tags: #Hacking #Enumeration #NFS 

## Nmap

```bash 
# Kali Tool 
❯ nmap -sV -T4 IP.0/24       # Escanear todos los puertos en una red 
❯ nmap -p 2049 IP            # Puerto 2049 es NFS 
```

## SuperEnum 

```bash 
# Kali Tool 
❯ ./superenum         # Ejecutamos el programa 
	❯ target.txt     # Colocamos el 'path' del archivo con la IP target 
```

## RPCScan 

```bash 
# Kali Tool 
❯ python3 rpc-scan.py IP --rpc    # Muestra info
```