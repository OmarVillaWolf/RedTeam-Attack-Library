# Enumeración local de Linux

Tags: #LocalEnumeration #PrivEsc #Linux #Meterpreter

## Enumeración de usuarios y grupos 

```bash 
# Que estamos buscando?

1. Usuario actual y sus privilegios 
2. Otros usuarios en el sistema 
3. Grupos 
```

## Comandos Linux

```bash 
❯ whoami                  # Muestra el nombre del usuario actual 

❯ groups root             # Muestra al grupo que pertenece un usuario en especifico 
❯ groups                  # Listas los grupos del SO

❯ cat /etc/passwd         # Muestra los usuarios en el SO y tienen un '/bin/bash'
❯ cat /etc/passwd | grep -v /nologin    # v = Excluir

❯ ls -al /home            # Podemos ver los directorios '/home' de los demas usuarios 

❯ last                    # Muestra los ultimos usuarios loggeados en el SO 
❯ lastlog                 # Muestra los usuarios loggeados en el SO
```

## Con Meterpreter

```bash 
# Si no te sale en el background la sesion de Meterpreter, se hace lo siguiente
❯ sessions -u <ID>         # Colocas el ID de la sesion actual, u = upgrade
❯ sessions <ID>            # Usas la sesion con el ID que te hayas creado en el upgrade anterior 

❯ getuid                   # Muestra el user ID (0 = root)
```