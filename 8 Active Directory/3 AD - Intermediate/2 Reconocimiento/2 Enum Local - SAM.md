# Enumeración local de SAM 

Tags: #AD #SAM #LocalEnumeration #PowerView 

```powershell 
❯ $env:UserName        # Nombre del usuario 

❯ $env:UserDomain      # Identificar el dominio 

❯ $env:ComputerName    # Identificar el nombre del computador 
❯ whoami               # Mostrar el dominio\usuario actual 
```

```powershell
! Usuario local 

# Enumeración de grupos locales en la máquina actual 
❯ Get-LocalGroup | select Name, ObjectClass, Principalsource, sid 

# Identificar usuarios (miembros) en el grupo 'Administrators' de forma local
❯ Get-LocalGroupMember -Group Administrators 
```

## PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```bash 
El módulo de PowerView puede llegar a generar alertas inofensivas en los sistemas de seguridad 
```

```powershell 
❯ Import-Module .\PowerView.ps1       # Importar el módulo 
```

```powershell 
! Usuario de dominio (AD)

# Enumeración de grupos locales 
❯ Get-NetLocalGroup       # Mirar los grupos locales de la máquina 

# Obtener los grupos de AD a los que pertenece el usuario especificado
❯ Get-NetGroup -UserName "user" | select name  
	# user = Usuario actual que pertenece al AD 

# Listar los miembros del grupo local Administrators, indicando si son grupos y si pertenecen al dominio
❯ Get-NetLocalGroupMember -GroupName Administrators | select-Object MemberName, IsGroup, IsDomain
```