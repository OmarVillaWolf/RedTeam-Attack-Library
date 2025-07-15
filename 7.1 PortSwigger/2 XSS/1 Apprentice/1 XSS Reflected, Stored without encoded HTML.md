# XSS Reflejado y almacenado sin codificación HTML 

Tags: #XSS #PortSwigger #JavaScript #XSS-Stored #XSS-Reflected 

## ¿Dónde detectar posibles puntos de Cross-Site Scripting (XSS)?

```bash 
Puntos clave:

1. Cualquier entrada que se refleje en la respuesta HTML:
    - Comentarios, formularios, campos de búsqueda, chats, perfiles de usuario, etc.
    - Parámetros en URLs (`GET`), como `/search?query=algo`.
        
2. Reflected XSS:
    - El input se refleja directamente en la respuesta (sin sanitizar) → prueba con `<script>alert(1)</script>`.

3. Stored XSS:
    - El payload se guarda en el servidor (como en una base de datos) y se ejecuta al cargar otra página.
    - Ej: en foros, comentarios, perfiles de usuario, etc.
        
4. DOM-based XSS:
    - El JavaScript del lado cliente toma datos de la URL y los usa sin sanitizar en el DOM.
    - Ej: `document.location`, `document.write()`, `innerHTML`, etc.
        
5. Falta de sanitización/escape:
    - Si puedes inyectar HTML/JS en la respuesta, es señal de que falta validación o escaping.
```

```bash 
1. HTTPOnly: Es una bandera de seguridad que se puede asignar a una 'cookie'. Sirve para evitar que esa cookie sea accesible desde JavaScript en el navegador.
2. Secure: Solo se envía por HTTPS.
3. SameSite: Reduce ataques CSRF.
4. Path y Domain: Limitan el alcance de la cookie.
```

```html  
// Primero ver si se puede ingresar código HTML 
	<h2>hola</h2>                     # Subtitulo 
	<marquee>Probando</marquee>       # Hacer que el contenido salga de derecha a izquierda
```

## XSS reflejado 

```bash 
El payload se refleja directamente en la respuesta del servidor sin guardarse. Se ejecuta al instante, como parte de la URL o del cuerpo de una petición.
```

```javascript  
<script>     // Ingresar un alert con código JavaScript          
	alert("XSS Reflejado")
</script>    
```

```javascript 
<script>    // Mirar la Cookie de Sesión 
  alert(document.cookie);
</script>
```

## XSS almacenado 

```bash 
El payload se guarda en el servidor (por ejemplo, en la base de datos), y luego se ejecuta cuando otro usuario accede a esa parte del sitio.

Se ve: En foros, comentarios, chats, perfiles de usuario, nombres de productos, etc.
```

```javascript  
<script>     // Ingresar un alert con código JavaScript          
	alert("XSS Almacenado")
</script>    
```

```javascript 
<script>    // Mirar la Cookie de Sesión 
  alert(document.cookie);
</script>
```

