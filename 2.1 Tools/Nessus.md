# Nessus 

Tags: #Nessus #Escaneo 

```bash 
❯ https://localhost:8834/                      # Ingresar al servicio local
	# U - Villalobos94
```

Descargamos Nessus
* [Download-Nessus](https://www.tenable.com/downloads/nessus?loginAttempted=true)

```bash 
❯ chmod +x Nessus-10.5.4-debian6_amd64.deb      # Le damos permisos de ejecucion a Nessus
❯ dpkg -i Nessus-10.5.4-debian6_amd64.deb       # Forma de instalar Nessus en Linux
```

```bash 
# Iniciar el servicio de Nessus en Linux

❯ systemctl start nessusd.service             # Iniciamos el servicio en Linux         
❯ systemctl status nessusd.service            # Ver si esta corriendo el servicio 
```

```bash 
# Iniciar/Parar el servicio de Nessus en Windows 

❯ net start "Tenable Nessus"                  # Activamos Nesus
❯ net stop "Tenable Nessus"                   # Paramos Nessus
```


```bash 
# Discovery 
- Host Discovery            # Simple escaneo para descubrir host y puertos abiertos

# Vulnerabilities 
- Basic Network Scan        # Escaneo del sistema completo adecuado para cualquier host
- Web Application Test      # Escaneo para vulnerabilidades web publicas o no conocidas
```

## Exportar e importar reportes de Nessus a MSF

```bash 
# Debemos de exportar como 'Nessus' y nos gurdara un archivo .xml, esto en la plataforma de Nessus

# En la consola de Linux, hacemos lo siguiente 
❯ msfconsole                                      # Iniciamos el servicio de MSF
❯ db_import /home/kali/Downloads/ms3.nessus       # Importamos el archivo .xml de Nessus llamado 'ms3' 
	❯ hosts                                      # Miramos que salga la IP, SO correcta del archivo importado 
	❯ services                                   # Miramos los puertos abiertos y sus servicios 
	❯ vulns                                      # Mirar las vulnerabilidades de la IP target         
	❯ vulns -p 445                               # Mirar las vulnerabilidades de un puerto en especifico   
```

