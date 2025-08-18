# SSRF ciego con detección out-of-band

Tags: #SSRF #PortSwigger 

```bash 
En este lab se continua con el estudio de ataques SSRF, pero en este caso nos enfrentamos a una variante ciega donde no obtenemos respuesta directa de la interacción. Aprovechamos una funcionalidad del sistema que realiza peticiones basadas en el valor del encabezado Referer, utilizado por un software de analítica interna.

Se manipula este encabezado para incluir un dominio generado por Burp Collaborator, lo que permite detectar si el servidor realiza una solicitud saliente hacia dicho dominio. Aunque no veamos resultados en la respuesta de la aplicación, los logs de Burp Collaborator nos confirman que se ha producido una interacción, validando así la existencia de la vulnerabilidad. Esta técnica es fundamental para detectar SSRF en contextos donde no hay una retroalimentación directa.
```

```bash 
# Petición original 
	Referer: https://web.com/
```

```bash 
# Petición modificada con el SSRF que se encuentra en la cabecera 'Referer' y se usa BurpCollaborator 
	Referer: https://BURP-COLLABORATOR-SUBDOMAIN/
```