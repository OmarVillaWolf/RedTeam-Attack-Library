# Enumeración Remota de SAM 

Tags: #AD #SAM #PowerView  #RemoteEnumeration #Kali #RPCClient #Impacket 

La enumeración remota funciona sin permisos de administración en caso de encontrarnos en un Windows 7 o una versión inferior al server 2016 

```powershell 
# Enumeración de grupos de otra máquina, se necesita privilegios de Admin de dominio 
❯ Invoke-Command -ScriptBlock { Get-LocalGroupMember -Group Administrators } -ComputerName WS02
```

## PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

El módulo de PowerView puede llegar a generar alertas inofensivas en los sistemas de seguridad 

```powershell 
❯ Import-Module .\PowerView.ps1        # Importar el módulo 
```

```powershell 
! Usuario de dominio (AD) con permisos de administrador 

# Enumeración de grupos de otra máquina, se necesita privilegios de Admin de dominio 
❯ Get-NetLocalGroup -ComputerName WS02  
```

```powershell
! Usuario local 

# Enumerar usuarios que están actualmente logueados en una máquina 
❯ Get-NetLoggedon 

# Mirar las sesiones SMB activas en el host, es decir, usuarios conectados a la máquina a través de la red. '\\WS01\C$' 
❯ Get-NetSession    # Mirar usuarios e IPs 
```

## Kali - RPCCLIENT

Esta forma de enumeración remota de la SAM se hará desde Kali Linux 

```bash 
! Usuario de dominio (AD) con permisos de administrador 

❯ rpcclient -U "corp\Administrator%Password" WS01.corp.local 
	# U = Usuario de dominio y contraseña 
	# WS01 = Equipo al que se quiere logear (Se puede colocar la IP)
	
	❯ ? o help         # Mirar todas las funciones disponibles 
	❯ enumprivs        # Mirar los privilegios que existen en el host 
	❯ enumdomusers     # Enumeración de usuarios 
	❯ enumalsgroups builtin         # Enumerar grupos locales integrados (builtin groups) del sistema y su RID
	❯ queryaliasmem builtin <rid>   # Mostrar los miembros de un grupo local usando su RID = 0x220 = Administrators
	❯ lookupsids S-1-5-21-27432511...-500     # Conocer el nombre del usuario desde su sid 
``` 

## Impacket 

* [Impacket-Github](https://github.com/fortra/impacket)

```bash 
! Usuario de dominio (AD) con permisos de administrador 

# Enumerar remotamente la SAM de la máquina WS01  
❯ impacket-samrdump corp/administrator:'Password'@WS01.corp.local   

# Se quedará escuchando sesiones de red que se establezcan a la máquina WS01
❯ impacket-netview corp/administrator:'Password' -target WS01.corp.local
```