# Web Sites 

Tags: #PassiveRecon #OSINT 

```bash 
❯ https://builtwith.com/                               # Miramos las tecnologias que usa la web 
❯ https://centralops.net/co/                           # Muestra 'address, whois, DNS record' y la IP del dominio 
❯ https://dnslytics.com/reverse-ip/                    # Muestra el dominio de la IP
❯ https://spyonweb.com/                                # Muestra informacion 'whois'
❯ https://www.virustotal.com/gui/home/upload           # Detecta 'malware' en archivos, urls
❯ https://visualping.io/                               # Monitorea la web por si cambia algo
❯ https://www.backlinkwatch.com/index.php              
❯ https://viewdns.info/                                

❯ https://urlscan.io/                                  # Muestra info de la web   
❯ https://dnsdumpster.com/                             
❯ https://web-check.as93.net/                          
❯ https://crt.sh/                                      # Muestra los subdominios
 
❯ https://web.archive.org/           # Muestra las versiones anteriores de las paginas de las empresas (Wayback Machine)
❯ https://www.shodan.io/
```

```bash 
# Herramientas  

❯ Web Data Extractor            # Extrae informacion especifica como nombres, correos, telefonos de un sitio web 
❯ HTTrack Web Site Copier       # Clonas un sitio web completo 
```

## Kali tools 

```bash 
❯ cewl http://domain.com        # Extrae todas las palabras de un sitio web y las agrega a un diccionario
```

```bash 
❯ https://github.com/projectdiscovery/subfinder

❯ subfinder -d domain.com       # Muestra los subdomiios 
```

```bash 
❯ https://github.com/tomnomnom/assetfinder

❯ assetfinder domain.com                    # Muestra los subdominios
❯ assetfinder domain.com > domain.txt       # Guardas el resultado en un archivo de texto
```

```bash 
❯ https://github.com/tomnomnom/httprobe

❯ cat domain.txt | sort -u | httprobe -s -p https:443    # Verifica si los subdominios estan funcionando 
```

```bash 
❯ https://github.com/owasp-amass/amass

❯ amass enum -d domain.com                  
```

```bash 
❯ https://github.com/sensepost/gowitness/wiki/Installation

❯ gowitness file -f ./alive.txt -P ./pics --no-http      # Toma un screen de cada pagina 
```

## Automating Website 

```bash 
#!/bin/bash 
# Use the first argument as the domain name 
domain=$1 
# Define colors 
RED="\033[1;31m" 
RESET="\033[0m" 

# Define directories 
base_dir="$domain" 
info_path="$base_dir/info" 
subdomain_path="$base_dir/subdomains" 
screenshot_path="$base_dir/screenshots" 

# Create directories if they don't exist 
for path in "$info_path" "$subdomain_path" "$screenshot_path"; do 
	if [ ! -d "$path" ]; then 
		mkdir -p "$path" 
		echo "Created directory: $path" 
	fi 
done 

echo -e "${RED} [+] Checking who it is ... ${RESET}" 
whois "$domain" > "$info_path/whois.txt" 

echo -e "${RED} [+] Launching subfinder ... ${RESET}" 
subfinder -d "$domain" > "$subdomain_path/found.txt" 

echo -e "${RED} [+] Running assetfinder ... ${RESET}" 
assetfinder "$domain" | grep "$domain" >> "$subdomain_path/found.txt" 

echo -e "${RED} [+] Checking what's alive ... ${RESET}" 
cat "$subdomain_path/found.txt" | grep "$domain" | sort -u | httprobe -prefer-https | grep https | sed 's/https\?:\/\///' | tee -a "$subdomain_path/alive.txt" 

echo -e "${RED} [+] Taking screenshots ... ${RESET}" 
gowitness file -f "$subdomain_path/alive.txt" -P "$screenshot_path/" --no-http
```