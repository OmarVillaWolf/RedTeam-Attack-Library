# Search Engines 

Tags: #Hacking #Footprinting #SearchEngines

## Google Hacking 

```bash 
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