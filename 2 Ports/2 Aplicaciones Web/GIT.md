# .GIT

Tags: #GIT 

**Git** es una herramienta para llevar el historial de cambios de un proyecto. Guarda versiones de los archivos para que puedas ver qué cambió, recuperar una versión anterior y colaborar con otras personas.
## Enumeración credenciales 
```bash 
# Enumerar en un dir si existe un .git 
❯ ls -la 
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