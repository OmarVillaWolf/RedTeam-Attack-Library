# Maltego 

Tags: #Hacking #Footprinting 

```bash 
# Kali tool 
❯ maltego 

Configuración: 
	1. Ejecutar la versión 'Maltego CE (Free)', aceptar los terminos 
	2. Registrase desde el siguiente enlace: https://www.maltego.com/ce-registration
	3. Ingresar con la cuenta creada, dar siguiente a todo y dejarlo por 'Default'
	4. En 'Ready' elegir la opción 'Open a blank graph and let me play'

Notas: 
	1. En 'Entity Palette' buscar en 'Infrastructure' la opción de 'Website' y arrastrarlo a 'New Graph'
	2. Click derecho y seleccionar 'All transforms' 
		1. Escoger 'To Domains [DNS]'
		2. Escoger 'To DNS Name [Using Schema diction...]'
		3. Escoger 'To DNS Name - SOA (Start of Authority)'
		4. Escoger 'To DNS Name - MX (mail server)'
		5. Escoger 'To DNS Name - NS (name server)'
		6. Escoger 'To IP Address [DNS]'
		7. Escoger 'To location [city, country]'
		8. Escoger 'To Entities from WHOIS [IBM Watson]'
```