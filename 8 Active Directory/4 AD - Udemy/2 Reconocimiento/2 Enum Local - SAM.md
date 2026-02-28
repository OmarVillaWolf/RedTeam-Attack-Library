# Enumeración local de SAM 

Tags: #AD #SAM #LocalEnumeration #PowerView 

```powershell 
❯ $env:UserName        # Nombre del usuario 

❯ $env:UserDomain      # Identificar el dominio 

❯ $env:ComputerName    # Identificar el nombre del computador 
❯ whoami 
```

```powershell
# Enumeración de grupos locales
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
❯ . .\PowerView.ps1                   # Cargar PowerView en memoria 
❯ Import-Module .\PowerView.ps1       # Importar el módulo 
```

```powershell 
# Enumeración de grupos locales 
❯ Get-NetLocalGroup       # Mirar los grupos locales de la máquina 

❯ Get-NetGroup -UserName "user" | select name   # Saber que grupos se tiene permiso localmente
	- grupo 'usuarios del dominio' = Administrar tu propio equipo 

❯ Get-NetLocalGroupMember -GroupName Administrators | select-Object MemberName, IsGroup, IsDomain  # Identificar usuarios (miembros) en el grupo 'Administrators' de forma local
```