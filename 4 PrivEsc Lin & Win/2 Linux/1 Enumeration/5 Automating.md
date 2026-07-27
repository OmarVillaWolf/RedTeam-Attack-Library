# Enumeración local de Linux

Tags: #LocalEnumeration #PrivEsc #Linux #Metasploit #Meterpreter

## LinEnum

* [LinEnum](https://github.com/rebootuser/LinEnum)

```bash 
❯ chmod +x LinEnum.sh                # Permisos paara ejecutar 
❯ ./LinEnum.sh                       # Ejecutar
```

## Enumeracion con Meterpreter 

```bash 
❯ sessions <ID>                                    # Regresas a la sesión de Meterpreter que tenias 

❯ background                                       # Colocamos la sesion en segundo plano 
	❯ search enum_configs                         # Enumera las configuraciones de Linux  
	❯ use post/linux/gather/enum_configs           
	❯ options 
	❯ set SESSION <ID>                            # Colocamos el ID de la sesión y lo encontramos con el comando 'sessions'
	❯ run 


❯ background                                       # Colocamos la sesion en segundo plano 
	❯ search enum_network                         # Enumera la informacion acerca de la red   
	❯ use post/linux/gather/enum_network            
	❯ options 
	❯ set SESSION <ID>                            # Colocamos el ID de la sesión y lo encontramos con el comando 'sessions'
	❯ run 


❯ background                                       # Colocamos la sesion en segundo plano 
	❯ search enum_system                          # Muestra informacion del sistema y usuarios Linux  
	❯ use post/linux/gather/enum_system           
	❯ options 
	❯ set SESSION <ID>                            # Colocamos el ID de la sesión y lo encontramos con el comando 'sessions'
	❯ run 


❯ background                                       # Colocamos la sesion en segundo plano 
	❯ search checkvm                              # Detecta si esta corriendo una VM en Linux  
	❯ use post/linux/gather/checkvm           
	❯ options 
	❯ set SESSION <ID>                            # Colocamos el ID de la sesión y lo encontramos con el comando 'sessions'
	❯ run 
```

