# Editores de texto 

Tags: #Editor #NVim #Neovim 

NVim Practica Online: 
* [NVim-Online](https://www.openvim.com/)
* [Vim Cheat Sheet](https://vim.rtorr.com/)

## Comandos NVim

```bash 
i                   # Modo inserción (ahí puedes empezar a escribir)
Esc                 # Sales del modo inserción
v                   # Modo visual 
Esc + Shift + 7     # Modo busqueda
Esc + Shift + :     # Modo sustitución

##  Todos estos comandos son en modo Esc
Alt + u         # Es como si hicieras un 'ctrl + z' en Windows
Tecla 'Home'    # inicio de la linea o pulsar '0'
Tecla 'End'     # Final de la linea o pulsar '$'
w               # Te mueves entre palabras hacia la derecha (una a la vez)
5 w             # Te mueves en este caso 5 palabras a la derecha (Tu eliges el numero de palabras a desplazar)
3 d w           # Eliminas tres palabras
d d             # Eliminas toda una linea
2 d d           # Eliminas dos lineas seguidas
o               # Creas una nueva linea 
p               # Pegas el contenido en la linea donde te encuentres 


##  Modo Visual
Shift + 4       # Seleccionas desde donde este hasta el final de la linea 
Shift + 4 + j   # Seleccionas desde donde estas al final de la linea y puedes ir bajando la seleccion con 'j'
y               # Copias lo seleccionado


Shift + ':'
	wq         # Guardas y sales de NVim (w=write, q=quit)
	q!         # Sales sin guardar
```

### Sustituciones en NVim

```bash
Esc + Shift + :     # Modo sustitución

❯ :%s/nologin/yeslogin/g      # Cambias el 'nologin' por el 'yeslogin'
❯ :%s/,/\r/g                  # Para aplicar una sustitución 

	# s = Sustitución
	# /,/ = Entre / es desde donde inicia (Delimitador)
	# \r = Retorno de carro y lo aplicara para la primer coincidencia 
	# /g = Para aplicar el filtro a todas las coincidencias
```