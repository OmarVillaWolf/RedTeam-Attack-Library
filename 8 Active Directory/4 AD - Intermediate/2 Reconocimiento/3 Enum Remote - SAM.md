# Enumeración Remota de SAM 

Tags: #AD #SAM #PowerView  #RemoteEnumeration #Kali #RPCClient #Impacket 


```bash 
La enumeración remota funciona sin permisos de administración en caso de encontrarnos en un Windows 7 o una versión inferior al server 2016 
```

```powershell 
# Enumeración de grupos de otra máquina, se necesita privilegios de Admin de dominio 
❯ Invoke-Command -ScriptBlock { Get-LocalGroupMember -Group Administrators } -ComputerName WS02
```

## PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```bash 
El módulo de PowerView puede llegar a generar alertas inofensivas en los sistemas de seguridad 
```

```powershell 
❯ . .\PowerView.ps1                    # Cargar PowerView en memoria 
❯ Import-Module .\PowerView.ps1        # Importar el módulo 
```

```powershell 
# Enumeración de grupos de otra máquina, se necesita privilegios de Admin de dominio 
❯ Get-NetLocalGroup -ComputerName WS02  
```

```powershell
# Enumerar que usuario se encuentra loggeado en un sistema 
❯ Get-NetLoggedon 

# Mirar las conexiones que hay hacía mi máquina. Esto se mira cuando hay conexiones hacía nuestra carpeta compartida '\\WS01\C$' 
❯ Get-NetSession    # Mirar usuarios e IPs 
```

## RPCCLIENT

```bash 
Esta forma de enumeración remota de la SAM se hará desde Kali Linux 

Es recomendable agregar la IP del DC de la siguiente manera:
	- Ir a 'Network Configuration > Wired Connection > Settings'
	- En 'IPv4 > Automatic (DHCP) addresses only'
	- Colocar la IP del servidor DNS que en este caso es la IP del DC 
	- Dar click en 'Save'
	- Nos desconectamos de la red y nos volvemos a conectar o el comando 'sudo dhclient -v'
```

```bash 
❯ rpcclient -U "corp\Administrator%Password" WS01.corp.local 
	# U = Usuario de dominio y contraseña 
	# WS01 = Equipo al que se quiere logear 
	
	❯ tap + Y          # Mirar todas las funciones disponibles 
	❯ enumprivs        # Mirar los privilegios del usuario en el sistema 
	❯ enumdomusers     # Enumeración de usuarios 
	❯ enumalsgroups builtin    
	❯ queryaliasmem builtin rid   # Consultar un grupo para identificar usuarios (sid)
		# rid = Es el número del grupo en este caso 'administrators 0x220' 
	❯ lookupsids S-1-5-21-27432511...-500     # Conocer el nombre del usuario desde su sid 
``` 

## Impacket 

* [Impacket-Github](https://github.com/fortra/impacket)

```bash 
# Enumerar remotamente la SAM de la máquina WS01  
❯ impacket-samrdump corp/administrator:'Password'%WS01.corp.local   

# Se quedará escuchando sesiones de red que se establezcan a la máquina WS01, se necesitan privilegios de admin 
❯ impacket-netview corp/administrator:'Password' -target WS01.corp.local
```