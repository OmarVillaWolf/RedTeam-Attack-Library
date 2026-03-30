# Creación de usuarios en el dominio con net

Tags: #AD #Windows #Dominio #Persistencia 

Estos comandos se emplean para gestionar cuentas de usuarios y grupos en sistemas Windows, y pueden ejecutarse desde la línea de comandos o mediante scripts. Cada comando cumple una función específica relacionada con la administración de cuentas de usuario y la asignación de membresías en un dominio de Active Directory. A continuación, se detalla la finalidad de cada uno:

```bash 
1. 'net user sephiroth Pass123 /add /domain': Este comando crea un nuevo usuario llamado 'sephiroth' en el dominio con la contraseña 'Pass123'. La opción '/add' indica que estamos añadiendo un nuevo usuario, y la opción '/domain' especifica que la operación debe ser realizada en el dominio en lugar de en la máquina local.
    
2. 'net localgroup Administrators sephiroth /add /domain': Con este comando, se añade el usuario 'sephiroth' al grupo de 'Administrators' del dominio. Esto otorgará al usuario 'sephiroth' todos los privilegios administrativos en todas las máquinas del dominio. La opción '/localgroup' se refiere a los grupos locales, aunque se use la opción '/domain' para afectar al grupo de administradores del dominio y no al de una máquina local específica.
    
3. 'net localgroup "Remote Desktop Users" sephiroth /add /domain': Este comando agrega el usuario 'sephiroth' al grupo 'Remote Desktop Users' en el dominio, permitiéndole iniciar sesión de forma remota a las máquinas del dominio que tienen habilitado y configurado el acceso remoto y que permitan a los miembros de este grupo conectarse.
    
4. 'net group "Domain Admins" sephiroth /add': Aquí, el usuario 'sephiroth' es añadido al grupo 'Domain Admins'. Los miembros de este grupo tienen permisos administrativos completos en todo el dominio, lo que les permite realizar cualquier acción en el dominio Active Directory. La falta de la opción '/domain' indica que este comando se debe ejecutar desde un controlador de dominio.
    
5. 'net group "Enterprise Admins" sephiroth /add': Este comando añade el usuario 'sephiroth' al grupo 'Enterprise Admins', que es un grupo a nivel de toda la empresa en un ambiente de bosque de Active Directory. Los miembros de este grupo tienen permisos para realizar cambios en toda la infraestructura del bosque, lo que incluye todos los dominios dentro del bosque. Este comando también debe ser ejecutado desde un controlador de dominio.
```

```powershell
# Estos comandos se ejecutan con un usuario de altos privilegios 

❯ net user omar Pass123 /add /domain                           # Agregar un usuario y su passwd al dominio 
❯ net localgroup Administrators omar /add /domain              # Agregar al grupo local de administrador
❯ net localgroup "Remote Desktop Users" omar /add /domain      # Agregar al grupo de RDP (Otra manera de tener persistencia)
❯ net group "Domain Admins" omar /add                          # Agregar al grupo de administradores del dominio
❯ net group "Enterprise Admins" omar /add                      # Agregar al grupo de enterprise admins 
```