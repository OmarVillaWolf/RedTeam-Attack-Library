# Reconocimiento Pasivo  

Tags: #PassiveRecon #Tool #Web

## Shodan 

```bash 
- https://www.shodan.io/           # Motor de búsqueda de dispositivos conectados a Internet (IoT, servidores, cámaras, routers). Se puede encontrar puertos en el dominio
```

## Censys 

```bash 
- https://search.censys.io/      # Es un motor de búsqueda que indexa toda la Internet pública (hosts IPv4, certificados TLS/SSL, dominios) mediante escaneo activo y pasivo. Se puede identificar puertos y servicios, versiones vulnerables de OpenSSL. Detecta malas configuraciones en SCADA, cámaras, AXFR
```

## Wayback Machine

```bash 
- https://web.archive.org/         # Archivo histórico de páginas web; permite ver versiones antiguas de sitios. También, se puede hacer un fuzzing 
```

## Zoomeye 

```bash 
- https://www.zoomeye.ai/          # Escáner y motor de búsqueda de infraestructura expuesta (similar a Shodan, más usado en Asia). Se puede encontrar puertos en el dominio
```

## Crt.sh 

```bash 
- https://crt.sh/                  # Visualiza certificados SSL/TLS públicos; útil para descubrir subdominios de un dominio.
```  

## Virustotal 

```bash 
- https://www.virustotal.com/gui/home/upload       # Analiza archivos, URLs e IPs con múltiples antivirus y motores de detección para identificar malware y amenazas. 

Notas:
	1. En la sección de 'Search' despues de escanear en la pestaña de 'Details' te puede mostrar subdominios 
```

## URLScan 

```bash 
- https://urlscan.io/      # Encontrar subdominios, detección de Endpoints, archivos ocultos, identificar tecnologías, phishing y clonación 
```

## DNSDumpster 

```bash 
- https://dnsdumpster.com/   # Encontrar subdominios, registros DNS y otros elementos relacionados con el dominio de un objeto. Además, se puede encontrar IPs asociadas, geolocalización, mapa visual de la infraestructura e información de terceros
```

## Intelligent X

```bash 
- https://intelx.io/         # Te permite encontrar información expuesta en la web pública, la darknet e incluso en redes como I2P y Tor, sin interactuar directamente con el objetivo. Por ejemplo: Buscar email, dominios, IPs, hashes, documentos, etc...
```

## Google Dork 

```bash 
# Es una búsqueda en Google con operadores avanzados para localizar archivos, vulnerabilidades, configuraciones inseguras, cámaras IP, contraseñas, etc.

- Encontrar 'archivos sensibles expuestos' (como .env, .sql, .xls, etc.)
- Detectar 'paneles de administración sin protección'
- Ubicar 'repositorios de backup'
- Identificar 'cámaras, routers y dispositivos IoT mal configurados'
- Descubrir 'errores o vulnerabilidades conocidas' en versiones específicas de CMS (WordPress, Joomla, etc.)

# Website 
* intitle     = Buscar en el titulo una palabra especifica 
* site        = Dominio a buscar 
* filetype    = Buscar un archivo con formato especifico 
* cache       = Mirar la version de cahce de la pagina web  
* allinurl    = Restringe resultados a paginas que contienen los terminos especificados en la busqueda (Se pueden ingresar varias palabras)
* inurl       = Restringe resultados a paginas que contienen la palabra especifica en la url (Una sola palabra)
* allintitle  = Restringe los resultados a paginas que contienen todos las palabras especificadas en el titulo (Se pueden ingresar varias palabras)
* inanchor    = Restringe los resultados a paginas que contienen anclas en el texto de la pagina web
* link = Busca sitios web o paginas que contiene enlaces al sitio web especifico 
* related = Muestra sitios web que son similares o relacionados a la url especifica
* info = Encuentra info para una pagina web especifica 
* location = Encuentra info para una locacion especifica 
  

# Ejemplos:
❯ intitle:login site:domain.com        # Buscar paginas de 'login' con el nombre del dominio 
❯ domain.com filetype:pdf ceh          # Buscar un archivo tipo pdf en un dominio especifico 
❯ cache: domain.com
❯ allinurl: domain.com career
❯ inurl: copy domain.com
❯ allintitle: detect malware  
❯ antivirus inanchor:Norton  
❯ link: domain.com
❯ related: domain.com
❯ info: domain.com
❯ location: domain.com
```