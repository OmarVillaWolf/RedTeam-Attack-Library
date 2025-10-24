# Nodejs

Tags: #NodeJs 

* [DVNA](https://github.com/appsecco/dvna)
* [Executing Shell Commands](https://stackabuse.com/executing-shell-commands-with-node-js/)

```javascript 
// Ejecutar el comando 'ls -la' en NodeJs

const { exec } = require("child_process"); 
exec("ls -la", (error, stdout, stderr) => { 
	if (error) { 
		console.log(`error: ${error.message}`); 
		return; 
	} if (stderr) { 
		console.log(`stderr: ${stderr}`); 
		return; 
	} 
	console.log(`stdout: ${stdout}`); 
});
```