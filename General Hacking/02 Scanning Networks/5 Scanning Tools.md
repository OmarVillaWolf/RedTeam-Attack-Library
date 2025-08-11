## Metasploit

Tags: #Hacking #NetworkScanning 

```bash 
# Kali Tool 
❯ service postgresql start     # Iniciar la DB 
❯ msfdb init                   # Iniciar la DB y la conecta a metasploit 
❯ service postgresql restart   # Reiniciar el servicio 
❯ msfconsole                   # Iniciar framework metasploit 
	❯ db_status               # Mirar que ya se encuentra conectada 

	❯ nmap -Pn -sS -A -oX Test IP/24     # Ejecutar el comando 'Nmap' dentro de 'Metasploit' 
	❯ db_import Test                     # Importar el archivo 
	❯ hosts                              # Mirar los hosts del archivo importado 
	❯ db_services                        # Mirar los servicios 
	❯ search portscan
		❯ use auxiliary/scanner/portscan/syn 
		❯ options 
		❯ set INTERFACE eth0
		❯ set PORTS 80 
		❯ set RHOSTS IP.5-23
		❯ set THREADS 50 
		❯ run 


		❯ use auxiliary/scanner/portscan/tcp
		❯ hosts -R            # Colocará los hosts que estan en la DB 
		❯ set RHOSTS IP
		❯ run 


		❯ use auxiliary/scanner/smb/smb_version 
		❯ options 
		❯ set RHOSTS IP.5-23
		❯ set THREADS 11 
		❯ run 
```