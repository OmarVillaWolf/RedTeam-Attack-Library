# Creacion de usuarios 

Tags: #Persistencia #Linux 

En Linux, un usuario puede ser creado por el administrador del sistema (root) utilizando comandos desde la terminal. Este proceso implica generar la cuenta y su entorno básico, asignar una contraseña para autenticar al usuario y, de ser necesario, otorgarle permisos administrativos al agregarlo al grupo de sudoers. Este método es eficiente para configurar cuentas nuevas y administrar permisos en el sistema de manera segura y controlada.

```bash 
❯ useradd -m nombre_usuario          # Crear un usuario y su directorio 'home'
❯ passwd nombre_usuario              # Asignar password al usuario
❯ usermod -aG sudo nombre_usuario    # Dar permisos de 'Root' (Opcional)
```