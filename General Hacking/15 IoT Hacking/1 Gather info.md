# Recolectar información IoT

Tags: #Hacking #IoT 

## Web

```bash
# Web Tool
❯ www.whois.com/Whois/         # Mirar la info de hostname, IP
❯ www.exploit-db.com/google-hacking-database    # Usar la DB de Google para buscar dispositivos IoT


# Tambien se puede buscar en Google
❯ "login" intitle:"scada login" 
❯ intitle:"index of" scada 


# Shodan
❯ https://account.shodan.io/login     # Shodan sirve para buscar dispositivos IoT
# Usando el buscador de Shodan
	❯ port:1883                      # Buscar el servicio MQTT 
	❯ port:502                       # Buscar el servicio Modbus ICS/SCADA
	❯ "schneider Electric"           # Buscar sistemas SCADA usando el nombre del PLC 
	❯ SCADA Country "US"             # Buscar sistemas SCADA usando geolocalización
```