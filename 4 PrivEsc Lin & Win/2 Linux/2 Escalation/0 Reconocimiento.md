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
❯ netstat -nat       # Mirar los puertos abiertos 

# Si se encuentra una password utilizarla de la siguiente manera ya que muchas veces funciona de una manera o de otra 
❯ ssh user@IP        # Conectarse por SSH desde Kali 
❯ su user            # Cambiar de usuario dentro del server víctima 
```

## Reconocimiento específico 

```bash 
❯ sudo -l                          # Sudoers  
❯ find \-perm -4000 2>/dev/null    # Binarios SUID
❯ systemctl list-timers            # Mirar si hay tareas a punto de ejecutarse 
❯ cat /etc/crontab                 # Mirar tareas Cron 
❯ getcap -r / 2>/dev/null          # Capabilities 
❯ ps -faux                         # Listar procesos 
❯ ps -eo user,command              # Mirar comandos en tiempo real 
❯ ss -nltp                 # Mirar puertos abiertos (Usar SSH, Ligolo, Chisel para traerlos a Kali)


# Si se encuentra un archivo ejecutable 
❯ strings file.sh                  # Mirar las cadenas de caracteres imprimibles  
	❯ strings -e l file.sh        # Mirar cadenas adicionales 
```