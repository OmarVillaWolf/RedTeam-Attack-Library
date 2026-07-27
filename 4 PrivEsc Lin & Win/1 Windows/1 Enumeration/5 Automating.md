# Enumeración local de Windows 

Tags: #LocalEnumeration #PrivEsc #Windows #Metasploit #Meterpreter

## Enumeración local Automática (Tools) en Windows 

### Enumeración con Meterpreter 

```bash 
❯ sessions <ID>                                    # Regresas a la sesión de Meterpreter que tenias 

❯ background                                       # Colocamos la sesion en segundo plano 
	❯ search win_privs                            # Enumera los privilegios de Windows 
	❯ use post/windows/gather/win_privs           
	❯ options 
	❯ set SESSION <ID>                            # Colocamos el ID de la sesión y lo encontramos con el comando 'sessions'
	❯ run 


❯ background                                       # Colocamos la sesion en segundo plano 
	❯ search enum_logged                          # Muestra información de los usuarios loggeados en el registro de Windows 
	❯ use post/windows/gather/enum_logged_on_users
	❯ options 
	❯ set SESSION <ID>
	❯ run


❯ background                                       # Colocamos la sesion en segundo plano 
	❯ search checkvm                              # Detecta si es un entorno de maquina virtual en Windows 
	❯ use post/windows/gather/checkvm
	❯ options 
	❯ set SESSION <ID>
	❯ run


❯ background                                       # Colocamos la sesion en segundo plano 
	❯ search enum_applications                    # Enumera las aplicaciones instaladas en el sistema 
	❯ use post/windows/gather/enum_applications
	❯ options 
	❯ set SESSION <ID>
	❯ run


❯ background                                       # Colocamos la sesion en segundo plano 
	❯ search enum_computers                       # Enumera los sistemas que estan conectados a la red 
	❯ use post/windows/gather/enum_computers
	❯ options 
	❯ set SESSION <ID>
	❯ run


❯ background                                       # Colocamos la sesion en segundo plano 
	❯ search enum_patches                         # Enumera los parches instalados 
	❯ use post/windows/gather/enum_patches
	❯ options 
	❯ set SESSION <ID>
	❯ run


❯ background                                       # Colocamos la sesion en segundo plano 
	❯ search enum_shares                          # Enumera los recursos SMB en Windows
	❯ use post/windows/gather/enum_shares
	❯ options 
	❯ set SESSION <ID>
	❯ run
```