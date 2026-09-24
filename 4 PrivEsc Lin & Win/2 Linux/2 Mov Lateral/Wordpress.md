# Wordpress

Tags: #WordPress #MovimientoLateral #PrivEsc 

WordPress es un CMS de código abierto (PHP + MySQL) especializado en blogs y sitios pequeños/medianos, más simple que Joomla. Usa temas y plugins para extender funcionalidad, almacenando todo en base de datos. Su archivo crítico `wp-config.php` contiene credenciales de BD. Es vulnerable a LFI en parámetros como `/index.php?p=` que permiten leer archivos del sistema, credenciales y logs del servidor.
### Rutas web importantes
```
/readme.html                     → Versión de WordPress
/wp-login.php                    → Panel de login
/wordpress/wp-login.php          → Instalación en subdirectorio
/wp-admin/admin.php              → Panel de administración
/wp-json/wp/v2/users/            → Enumerar usuarios sin auth (JSON)
/wp-content/plugins/             → Directory listing de plugins
/xmlrpc.php                      → Si expuesto → fuerza bruta sin límite
/?author=1                       → Enumeración manual de usuarios
/?author=2                       → Incrementar para más usuarios
```

### Archivos críticos por consola
```
/var/www/html/wp-config.php           → Credenciales de DB → leer siempre
/usr/share/wordpress/wp-config.php    → Ruta alternativa
/var/www/html/wp-config.php           → Ruta con credenciales 
/etc/apache2/sites-enabled/wordpress.conf  → Configuración Apache
/var/www/html/wp-content/uploads/     → Archivos subidos → buscar webshells
/var/www/html/wp-content/plugins/     → Plugins instalados
/var/www/html/wp-content/themes/      → Temas instalados
```

### wp-config.php — Datos críticos
```bash
❯ cat /var/www/html/wp-config.php | grep -E "DB_NAME|DB_USER|DB_PASSWORD|DB_HOST"
# Credenciales de la base de datos → reutilizar en MySQL, phpmyadmin
# Con esas credenciales → http://IP/phpmyadmin → usuarios y hashes en la DB
```