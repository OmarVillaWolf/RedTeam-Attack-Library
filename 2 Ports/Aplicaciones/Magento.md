# Magento 

Tags: #Magento 

## Enumeración de gestores de contenido (CMS) – Magento

Magento es una plataforma de comercio electrónico de código abierto, que se utiliza para construir tiendas en línea de alta calidad y escalables. Es una de las plataformas más populares para el comercio electrónico y es utilizado por grandes marcas como Nike, Coca-Cola y Ford.

Sin embargo, con la popularidad de Magento también ha surgido la preocupación por la seguridad. Una de las herramientas que veremos en esta clase es **Magescan**, una herramienta de escaneo de vulnerabilidades específica para Magento.

Magescan puede detectar vulnerabilidades comunes en Magento, incluyendo problemas con permisos de archivos, errores de configuración y vulnerabilidades conocidas en extensiones populares de Magento.

* [Magento-SQLI](https://github.com/vulhub/vulhub/tree/master/magento/2.2-sqli)

A continuación se proporciona el enlace directo al proyecto en Github:

-   **Magescan**: [https://github.com/steverobbins/magescan](https://github.com/steverobbins/magescan)

## Magento Enumeración 

Esta herramienta además de enumerar un servidor Magento.
**/administration/admin/index** Es la ruta del panel de autenticación de admin

```bash 
❯  php magescan.phar scan:all https://IP/
```