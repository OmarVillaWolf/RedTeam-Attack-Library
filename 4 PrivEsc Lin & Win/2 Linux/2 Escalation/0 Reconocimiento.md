# Reconocimiento 

Tags: #PrivEsc 

## Reconocimiento general

```bash 
1. Mirar archivos en '/home' que no sean muy normales que esten ahí
2. Mirar archivos de 'config' en rutas especificas 
3. Mirar el 'bash_history'

❯ whoami             # Mirar el usuario 
❯ ipconfig           # mirar las interfaces 
❯ id                 # Mirar los grupos 
❯ lsb_release -a     # Mirar la versión de Linux 
❯ uname -a           # Mirar la versión de kernel 
```

## Reconocimiento específico 

```bash 
❯ sudo -l                          # Sudoers  
❯ find \-perm -4000 2>/dev/null    # Binarios SUID
❯ systemctl list-timers            # Mirar si hay tareas a punto de ejecutarse 
❯ cat /etc/crontab                 # Mirar tareas Cron 
❯ getcap -r / 2>/dev/null          # Capabilities 
❯ ps -faux                         # Listar procesos 
❯ ss -nltp                         # Mirar puertos abiertos (Usar SSH, Ligolo, Chisel)


# Si se encuentra un archivo ejecutable 
❯ strings file.sh                  # Mirar las cadenas de caracteres imprimibles  
	❯ strings -e l file.sh        # Mirar cadenas adicionales 
```