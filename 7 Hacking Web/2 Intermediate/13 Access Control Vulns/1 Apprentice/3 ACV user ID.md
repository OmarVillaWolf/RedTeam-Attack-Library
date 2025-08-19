# Vulnerabilidades de control de acceso 

Tags: #AccessControl #BurpSuite 

## ID de usuario controlado por parámetro

```bash 
En este lab en el panel de cuenta, se modifica el parámetro id de la URL o la petición para apuntar a otro usuario.

Esto permite visualizar su información y obtener su API key sin necesidad de autenticación adicional, demostrando una escalada horizontal de privilegios.
```

```bash 
# Se puede cambiar el usuario si no esta sanitizado y obtener info sensible  
	https://web.com/my-account?id=omar
```

## ID de usuario impredecible controlado por parámetro

```bash 
En eset lab se identifica el GUID del usuario accediendo a una publicación suya y copiando su ID desde la URL.

Luego, se modifica el parámetro id en nuestra página de cuenta con ese identificador para obtener su API key, explotando así una escalada horizontal de privilegios basada en identificadores poco protegidos.


- GUID: Es un identificador único global, que es una cadena de texto que representa un identificador de clase (ID). Se utiliza para identificar elementos en diferentes sistemas, con una muy baja posibilidad de duplicación. 
```

```bash 
# Se puede cambiar el usuario si no esta sanitizado y obtener info sensible  
	https://web.com/my-account?id=46bebab3-ghof-46bf-9fd8-7890fcf8bfsd
```

## Filtración de datos en redirección con ID por parámetro 

```bash 
En este lab se modifica el parámetro id en nuestra página de cuenta para apuntar a otro usuario.

Aunque el servidor nos redirige, en el cuerpo de la respuesta se filtra la API key del otro usuario, permitiendo obtener su información sin autorización.
```

```bash 
# Existe una redirección al capturar la petición, por lo que antes de la redirección en la respuesta se puede observar la info del usuario
	http://web.com/my-account?id=omar
```

## Filtración de contraseña con ID por parámetro

```bash 
En este la se accede a nuestra página de cuenta y se modifica el parámetro id en la URL para apuntar al usuario 'administrator'.

En la respuesta del servidor, se filtra su contraseña dentro de un campo oculto del formulario. Utilizamos esta contraseña para iniciar sesión como administrador
```

```bash 
❯ ctrl + shift + c     # Inspeccionar el código

# Cambiar la parte de password 
	type="password" name="password" value="12345"
	
# Mirar la password en texto plano 
	type="text" name="password" value="12345"
```