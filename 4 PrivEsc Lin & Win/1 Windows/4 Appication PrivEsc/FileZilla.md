# FileZilla 

Tags: #PrivEsc #FileZilla #Windows 

FileZilla es un programa gratuito de código abierto que sirve para transferir archivos entre tu computadora y un servidor remoto a través de internet. Funciona con protocolos como FTP, SFTP y FTPS, y es muy usado para administrar páginas web.

```bash 
# FileZilla — Recuperación de credenciales guardadas

! FileZilla guarda credenciales de servidores FTP en XML en texto casi-claro
! Si encuentras una máquina Windows con FileZilla instalado → siempre revisar

RUTAS A REVISAR:
❯ type "C:\Users\<user>\AppData\Roaming\FileZilla\recentservers.xml"
❯ type "C:\Users\<user>\AppData\Roaming\FileZilla\sitemanager.xml"

FORMATO DEL ARCHIVO:
<Server>
    <Host>192.168.x.x</Host>
    <User>admin</User>
    <Pass encoding="base64">dGhpc2lzdGhlcGFzc3dvcmQ=</Pass>
</Server>

DECODEAR EN KALI:
❯ echo "dGhpc2lzdGhlcGFzc3dvcmQ=" | base64 -d

! Las credenciales de FileZilla suelen ser credenciales reutilizadas
  del mismo usuario del sistema → probar en RDP, SMB, WinRM

BUSCAR INSTALACIÓN:
❯ dir /s /b C:\Users\ 2>nul | findstr /i "filezilla"
❯ dir /s /b C:\Users\ 2>nul | findstr /i "recentservers\|sitemanager"
```

