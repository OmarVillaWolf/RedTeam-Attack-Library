# Creación de usuarios local con 'Net'

Tags: #Persistencia #Windows #Local

El comando `net` en Windows se utiliza para crear usuarios y asignarles privilegios elevados de manera eficiente desde la línea de comandos, siendo especialmente útil en tareas administrativas, automatización de procesos y pruebas de seguridad. Permite crear cuentas nuevas y agregarlas al grupo de administradores para otorgarles permisos elevados, facilitando configuraciones rápidas y la ejecución de scripts en entornos de gestión o auditorías.

```bash 
❯ net user omar P4ssw0rd /add                   # Creamos un usuario siendo 'NT Authority\System'
❯ net localgroup Administrators omar /add       # Agregar el usuario al grupo 'Administrators'
❯ net user Omar                                 # Mirar el grupo de un usuario en especifico
```