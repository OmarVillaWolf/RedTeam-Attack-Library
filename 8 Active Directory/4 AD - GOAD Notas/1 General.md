# Enumeración General 

Tags: #DC #NetExec #Kali 

## Enumeración inicial en la red 

```bash 
❯ nmap -sn IP.0/24      # Identificar los hosts en la red 

❯ rustscan -a IP --ulimit 5000 -- -sCV -oN targeted    # Escaneo de puertos y versiones a cada IP

❯ nxc smb IP.0/24       # Identificar el puerto 445 SMB en los hosts para mirar si tienen firma 
```

![[Pasted image 20260419215124.png]]