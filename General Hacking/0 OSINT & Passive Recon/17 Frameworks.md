# Frameworks

Tags: #PassiveRecon #Recon-ng

## Kali tools 

```bash 
❯ maltego 
❯ searchfy
❯ recon-dog 
❯ billcipher.py 
❯ grecon
❯ th3inspector
❯ raccoon 
❯ orb
❯ FOCA
❯ Spyse
❯ https://hunch.ly/                      # Te subraya el texto a buscar en las paginas que visites  
```

## Recon-ng

```bash
❯ recon-ng          # Tool con muchos modulos con apis para hacer busquedas, similar al uso de 'Metasploit'

❯ marketplace search                     # Miramos los modulos disponibles 

❯ marketplace install hackertarget       # Instalamos el modulo para obtener dominions, IPs
❯ modules load hackertarget              # Cargamos el modulo 
	❯ info                              # Opciones a agregar 
	❯ options set SOURCE domain.com
	❯ run 
	❯ show profiles                     # Muestra el resumen 
	❯ back                              # Regresamos al menu principal 


# Modulo para mostrar todas las web en donde se encuentra ese usuario 
❯ marketplace install profiler           # Instalamos el modulo
❯ modules load profiler                  # Cargamos el modulo
	❯ info                              # Opciones a agregar 
	❯ options set SOURCE username       
	❯ run 
	❯ show profiles                     # Muestra el resumen
```

