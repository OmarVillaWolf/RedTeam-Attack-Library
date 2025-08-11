# Recon-ng 

Tags: #Hacking #Footprinting 

```bash
# Kali tool 
❯ recon-ng                        #  
	❯ help                       # Panel de ayuda 
	❯ marketplace install all    # Instalar todas las herramientas 
	❯ module search              # Mirar los modulos instalados 
	❯ workspaces create name     # Crear un espacio de trabajo 
	❯ workspaces list            # Mirar los diferentes espacios de trabajo
	❯ db insert domains 
		❯ www.domain.com        # Agregar el dominio a usar 
	❯ show domains               # Mirar los dominios agregados 
	❯ modules load brute         # Mirar los módulos relacionados
	❯ modules load recon/domains-hosts/brute-hosts   # Cargar el módulo a usar. Este módulo busca los hosts 
		❯ run                   # Ejecutas el ataque 
		❯ back                  # Regresas 
	❯ modules load recon/domains-hosts/bing_domain_web   # Resolver hosts usando el módulo bing 
		❯ run 
		❯ back 
	❯ modules load reverse_resolver    # Mirar los módulos relacionados 
	❯ modules load recon/hosts-hosts/reverse_resolve   # Efectuar un lookup reverso  
		❯ run 
		❯ show hosts     # Muestra los hosts encontrados 
		❯ back
	❯ modules load reporting     # Mirar los módulos relacionados 
	❯ modules load reporting/html 
		❯ options set FILENAME /home/kali/Desktop/results.html
		❯ options set CREATOR Omar 
		❯ options set COSTUMER Name
		❯ run 
		❯ back 
```

```bash 
# Kali tool 
❯ recon-ng  
	❯ workspaces create name  
	❯ modules load recon/domains-contacts/whois_pocs    # Utilizara la base de ARIN Whois RWS
		❯ options set SOURCE domain.com 
		❯ run 
		❯ back 
```

```bash 
# Kali tool 
❯ recon-ng  
	❯ workspaces create name  
	❯ modules load recon/domains-hosts/hackertarget    # Lista los subdominios y direcciones IP 
		❯ options set SOURCE domain.com 
		❯ run 
		❯ back 
```