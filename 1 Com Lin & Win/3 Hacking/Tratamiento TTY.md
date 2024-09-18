# Diferentes códigos

## Tratamiento de la pseudo-consola

```bash
❯ python3 -c 'import pty;pty.spawn("/bin/bash")'         # Obtenemos una 'bash', este comando es por si no sirve el del 'script'

❯ script /dev/null -c bash                               # Inicio del tratamiento de la consola 
❯ Ctrl + z
❯ stty raw -echo; fg
❯ reset xterm

# Despues cambiamos esto para poder hacer Ctrl+ l y Ctrl + c
❯ echo $SHELL                                            # Para ver la ruta de shell y ver que valor tiene **/usr/bin/nologin**
❯ export SHELL=bash o ❯ export SHELL=/bin/bash           # Hacemos que shell ahora valga bash
❯ export TERM=xterm                                      # Para poder hacer Ctrl +c y Ctrl + l (l=ele)

❯ export TERM=xterm-256color                             # Para que la shell tenga colores 
	❯ source /etc/skel/.bashrc

# Ahora para modificar las dimensiones de Vim/nano debemos hacer lo siguiente.
❯ stty size                                              # Miramos las dimensiones de la consola
❯ stty rows 51 columns 189                               # Modificamos las dimensiones de la consola Vim/Nano

# Ajustar tamaño de la consola 
❯ xrandr -s 1                                            # Modificamos las dimensiones de la consola en general (Tamaño del monitor que estemos usando)
	❯ Win + Shift + r                                     # Recargamos los cambios en la Kitty 
```

## Obtener una consola interactiva en Linux desde Meterpreter

```bash 
# Consola 'Bash' desde Meterpreter
❯ cat /etc/shells         # Muestra las shells que estas instaladas 
❯ /bin/bash -i            # Nos provee una Shell 'bash' interactiva 

# Consola 'Bash' desde Meterpreter con Python
❯ python --version        # Mirar la version de Python, para despues obtener una Shell
❯ python -c 'import pty;pty.spawn("/bin/bash")'   # Obtenemos una consola 'bash'

# Consola 'Bash' desde Meterpreter con Perl
❯ perl -e 'exec "/bin/bash";'
❯ perl: exec "/bin/bash";

# Consola 'Bash' desde Meterpreter con Ruby
❯ ruby: exec "/bin/bash"
```