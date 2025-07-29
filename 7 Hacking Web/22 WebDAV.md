# Enumeración y explotación de WebDAV

Tags: #WebDAV #OWASP #Explotacion 

**WebDAV** (**Web Distributed Authoring and Versioning**) es una extensión del protocolo HTTP que permite a los usuarios **acceder** y **manipular** **archivos** en un servidor web a través de una conexión segura.

Cuando hablamos de enumerar un servidor WebDAV, a lo que nos referimos es al proceso de recopilar información sobre los recursos disponibles en el servidor WebDAV. Los atacantes pueden utilizar herramientas de enumeración de WebDAV para buscar recursos protegidos en el servidor, como archivos de configuración, contraseñas y otros datos confidenciales. Los atacantes pueden utilizar la información recopilada durante la enumeración para planificar ataques más sofisticados contra el servidor.

Asimismo, esta fase inicial de reconocimiento implica el intentar identificar las **extensiones de archivo** permitidas en el servidor. Una vez detectadas las extensiones de archivo permitidas, los atacantes pueden aprovecharse de esto para cargar y ejecutar archivos que contengan código malicioso. Si los archivos maliciosos se cargan y ejecutan con éxito en el servidor web, los atacantes pueden obtener acceso no autorizado al servidor y comprometer la seguridad del sistema.

Una de las herramientas que vemos en esta clase es **Davtest**. La herramienta Davtest es una herramienta de línea de comandos que se utiliza para realizar pruebas de penetración en servidores WebDAV. Davtest puede utilizarse para enumerar recursos protegidos en un servidor WebDAV, así como para probar la configuración de seguridad del servidor. Davtest también puede utilizarse para probar la autenticación y la autorización del servidor, y para detectar vulnerabilidades conocidas.

Otra de las herramientas que vemos en esta clase es **Cadaver**. Cadaver es otra herramienta de línea de comandos que se utiliza para interactuar con servidores WebDAV. Cadaver permite a los usuarios navegar por los recursos del servidor, cargar y descargar archivos, y ejecutar comandos en el servidor. Cadaver también puede utilizarse para realizar pruebas de penetración en servidores WebDAV, como la enumeración de recursos protegidos y la explotación de vulnerabilidades conocidas.

Para prevenir la enumeración y explotación de WebDAV, es importante que los administradores de sistemas implementen medidas de seguridad adecuadas en el servidor. Esto puede incluir la limitación de los recursos disponibles en el servidor y la utilización de autenticación y autorización fuertes. Además, es importante que los usuarios protejan sus contraseñas y eviten el uso de contraseñas débiles o fáciles de adivinar.

A continuación, se proporciona el enlace al proyecto de Github el cual estaremos usando en esta clase para desplegar un entorno vulnerable con el que poder practicar:

-   **WebDav**: [https://hub.docker.com/r/bytemark/webdav](https://hub.docker.com/r/bytemark/webdav)


## WebDAV 

Al momento de querer entrar a una pagina Web. En el WebDAV nos podemos encontrar con este tipo de login. 

![](Pasted%20image%2020230516144325.png)

Para darnos cuenta que es un WebDAV podríamos usar la herramienta de Whatweb que nos ayuda a identificar las tecnologías que corren detrás de la web, así como el gestor de contenidos, etc...

```bash
❯ whatweb http://127.0.0.1                  # Nos dara una breve descripcion del gestor de contenidos del puerto 80

	# Encontrariamos el 'WWW-Authenticate[WebDAV]'
	# Tambien podriamos encontrar el 401 Unauthorized
```

En donde podríamos colocar las credenciales comunes que son 'admin:admin'. Pero si vemos que no podemos entrar podríamos usar la siguiente herramienta si es que disponemos de credenciales validas. 

```bash 
❯ davtest -url http://127.0.0.1 -auth admin:admin     # Debemos de tener el usuario y passwd validos para poder usar la tool

	# 1er admin = Usuario 
	# 2do admin = passwd
```

Para poder obtener la passwd, podríamos hacer lo siguiente:

```bash 
❯ cat /usr/share/wordlists/rockyou.txt | while read password; do response=$(davtest -url http://127.0.0.1 -auth admin:$password 2>&1 | grep -i succed); if [ $response ]; then echo "[+] La passwd correcta es: $password"; break; fi; done

# Ataque de Fuerza Bruta para encontrar la passwd con el comando davtest
```

Podemos usar esta otra herramienta para , esta tool también nos pedirá la autenticación. 

```bash 
❯ cadaver http://127.0.0.1     # Sirve para subir archivos, descargar contenido, etc... 

	❯ mkdri test              # Podriamos crearnos dir en los cuales podemos colocar un recurso 
	❯ cd test 
	❯ put webdav.txt          # Podemos subir un archivo 
```
