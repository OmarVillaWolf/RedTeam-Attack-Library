# Restricted Bash 

Tags: #SSH 

## Saber si es una restricted bash 
```bash 
# Cuando conectas y ejecutas algo, vas a ver errores como:

$ cd /tmp
-rbash: cd: restricted

$ export PATH=/bin:$PATH
-rbash: PATH: readonly variable

$ ls /etc/passwd
-rbash: /etc/passwd: No such file or directory  # (por restricción de rutas, no porque no exista)

$ /bin/bash
-rbash: /bin/bash: restricted
```

## Comandos de diagnóstico (una vez dentro)
```bash 
❯ echo $0
# Si devuelve "rbash" o "-rbash" → confirmado

❯ echo $PATH 
# Muestra un PATH muy corto 

❯ echo $SHELL
# Muestra la shell configurada (aunque a veces no coincide con la que realmente corre)

❯ cat /etc/passwd | grep <user>
# Si tienes lectura, busca el shell asignado al usuario:
# user:x:1000:1000::/home/user:/bin/rbash

❯ compgen -c 2>/dev/null | wc -l
# Si el número de comandos disponibles es sospechosamente bajo (rbash suele limitar PATH)

❯ type cd
# rbash: cd is a shell builtin, pero puede estar bloqueado igual

❯ cd /bin 
# Muestra restricted 
```

## Ejecución remota de comandos 
```bash
# Ejecutar comando remoto sin shell interactiva
❯ ssh user@<IP> 'whoami'  
❯ sshpass -p 'passwd' ssh user@<IP> whoami

# Bypass de restricted shell (rbash mal configurada)
❯ ssh user@<IP> /bin/sh
❯ ssh user@<IP> bash  
	❯ script /dev/null -c bash   # Fuerza PTY interactiva 

❯ ssh user@<IP> -t bash --noprofile   # Fuerza PTY interactivo, evita cargar .bashrc/profile con restricciones
❯ ssh user@<IP> 'python3 -c "import pty; pty.spawn(\"/bin/bash\")"'


NOTAS:
	- Usuario válido
	- Acceso SSH permitido
```
