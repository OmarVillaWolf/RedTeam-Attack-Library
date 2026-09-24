# Joomla 

Tags: #Linux #MovimientoLateral #PrivEsc 

Joomla es un CMS de código abierto (PHP + MySQL) que permite crear y gestionar sitios web dinámicos sin programación. Funciona con componentes, módulos y plugins extensibles, almacenando contenido, usuarios y configuración en base de datos. Su archivo `configuration.php` contiene credenciales sensibles. Es similar a WordPress pero más complejo, usado para portales corporativos y sitios medianos/grandes.

## Rutas comúnes 
```bash 
/var/www/html/configuration.php     # Archivo principal con credenciales de la base de datos
/var/www/html/wp-config.php
/var/www/html/.env 
/var/www/html/etc/mysql/my.cnf      # Configuración de MySQL. Puede revelar contraseñas o accesos alternos
```