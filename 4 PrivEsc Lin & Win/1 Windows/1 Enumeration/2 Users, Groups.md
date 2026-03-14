# Enumeración local de Windows 

Tags: #LocalEnumeration #PrivEsc #Windows  #Meterpreter


```bash 
# Que estamos buscando?

1. Usuario actual y sus privilegios
2. Información adicional del usuario
3. Otros grupos en el sistema 
4. Grupos
5. Miembros del grupo de administrador 
```
## Comandos Windows 

```bash
❯ whoami                   # Mirar el nombre del usuario
❯ whoami /priv             # Mirar los privilegios 'Token' del usuario 
❯ whoami /all              # Mirar toda la info del usuario 
```

```bash 
❯ query user               # Mirar si hay algun usuario loggeado
```

```bash
❯ net user                             # Mirar todos los usuarios existentes y sus grupos de forma local
❯ net user <User>                      # Mirar los grupos de un usuario especifico como 'administrator' de forma local
❯ net user admin password123           # Cambiar la passwd al usuario admin siendo NT Authority\System de forma local

❯ net group "Group"                    # Mirar los grupos del usuario 
❯ net localgroup "administrators"      # Mirar los miembros del grupo administrador de forma local


❯ net user omar P4ssw0rd /add               # Crear un usuario siendo NT Authority\System de forma local
❯ net localgroup Administrators omar /add   # Agregar al usuario al grupo local 'Administrators'
```
## Comandos con Meterpreter 

```bash 
❯ getprivs                        # Enumeramos los privilegios del usuario actual  

❯ sessions <ID>                   # Regresas a la sesión de Meterpreter que tenias

❯ background                      # Tambien podemos usar (Ctrl + z) para poner la sesión en segundo plano y poder buscar exploits
	❯ search logged_on           # Exploit para enumerar usuarios que estan loggeados en Windows 
	❯ use post/windows/gather/enum_logged_on_users
	❯ options 
	❯ set SESSION <ID>           # Colocamos el ID de la sesión en background y lo podemos obtener con el comando 'sessions'
	❯ run 


❯ shell                           # Generas una consola para comandos Windows 
```

