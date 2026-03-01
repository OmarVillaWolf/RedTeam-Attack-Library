# Amass 

Tags: #Fuzzing #Web #Amass #SubDomains 

```bash 
❯ https://github.com/owasp-amass/amass      # Descargar la tool 
```

```bash 
❯ amass -h     # Panel de ayuda 
```

```bash 
❯ amass intel -whois -d domain.com          # Provee todos los dominios que estan asociados al dominio dado 
❯ amass intel -ipv4 -d domain.com           # Obtener la IP del dominio 
```

```bash 
❯ amass enum -d domain.com                  # Enumerar subdominios para mirar si existen aplicaciones distintas, tecnologias diferentes, vulnerabilidades propias y credenciales 
❯ amass enum -passive -d domain.com 
❯ amass enum -active -brute -d domain.com -w /usr/share/wordlists/amass/subdomains.lst      # Ataque de diccionario para buscar subdominios 
❯ amass enum -active -d domain.com -src      # Enumerar subdominios desde la diferentes motores de busqueda 
```