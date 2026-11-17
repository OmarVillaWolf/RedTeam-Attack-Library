# Printer Configuration portal 

Tags: #Printer #Windows #SSTI #Python #Flask 


Es la interfaz web de administración que traen las impresoras y multifuncionales de red. El fabricante suele llamarla Embedded Web Server (EWS). Entras por navegador a la IP de la impresora, normalmente en el puerto 80/443 o algún puerto alto, y desde ahí configuras:

Red: IP, DNS, SNMP
Libreta de direcciones / LDAP: para buscar usuarios del dominio al escanear a correo
SMTP: para scan-to-email
SMB/FTP: para scan-to-folder
Usuarios, firmware, logs de trabajos de impresión

```bash 
# Credenciales por defecto 
admin:admin 
```

```bash 
Opción 1:
# Si se tiene python y flask buscar donde se refleja alguna entrada y es ahi donde se prueba el payload:
❯ {{7*7}}   # Si muestra un 49 es vulnerable 

Opción 2:
# Si se tiene una entrada para hacer ping a una url se puede obtener el hash NetNTLMv2 usando un responder
❯ responder -I tun0 
```